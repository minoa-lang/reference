# Composite representation

All user-defined composite types have a represetation that specifies how the type is laid out.
The possible representations are:
- `minoa`
- `extern`
- `transparent`

While the representation of a type can affect the padding between fields, it does not change the layout of the fields themselves.
Meaning that if a field has a `C` representation within a type of a `minoa` representation, that field will keep its `C` representation.

## `minoa` representation [↵](#layout-representation)

This is the default representation of a type, meaning it cannot be manually set, instead the absence of other specifiers or related attributes indicate it.

This representation only provides a minimum number of guarantees about the layout of fields, these are the following:
- each field is properly aligned
- fields do not overlap
- the alignment of the type is at least that of hte field with the highest alignment, when not manually specified

The first guarantee means that the offset of a field will always be a multiple of the fields alignment.

The second guarantee means that the fields are ordered in such a way, that the offset plus the size of the field is less or equal to the offset of the next field in the type.
However, this does not means that zero-sized types will have a unique offset, multiple zero-sized types may be located at the same address, unless explicitly specified.

The third guarnatee ensures that the alignment of all fields can always be guaranteed, no matter where in the field order they are located.

There is no guarantee that the field will be laid out in the same order as defined within the type.

### Unique zero-sized address [↵](#minoa-representation-)

It is possible to ensure that a zero-sized type will be located at a unique address, and that no other field will overlap this address.

This is done using the [`@unique_addr` attribute].
The attribute will only have an effect on zero-sized types, and will possibly add 1 additional byte to the size of the structure, if this field cannot be located within the padding of the other fields.

> _Example_
> ```
> struct Bar;
> 
> struct Foo {
>     b0: Bar,
>     b1: Bar,
>     @unique_addr
>     b2: Bar,
>     b3: Bar,
> }
> ```
> In this type, both `b0`, `b1`, and `b4` can be located at the same address, but it is guaranteed to be at a different address than `b2`.
> This also means that while this type only contains zero-sized fields, that he type will have a size of 1 byte.

### Field priority [↵](#minoa-representation-)

Since by default, there is no guarnatee about the orderding of field with a composite type, the compiler may lay these fields out in a way that is not optimal for a given usecase.
To ensure the programmer can provide additional hits to the compiler on which field should be prioritized during layout, the [`@field_priority` attribute] can be used.

One of the reasons this might be useful, is in a case where a type would stretch across multiple cache lines, but only certain data is frequently used.
This allows this data to be laid out at the start of the structure, only requiring a single cache line to be loaded to access the common data.

The field priority takes a value in the range of `[0, 255]`, with 0 being the default for all fields.
Fields with a higher priority will be laid out first in the type.

> _Example_
> ```
> struct Foo {
>     big: [256]u8,
>     @field_priority(255),
>     important: u32,
> }
> ```
> Using the attribute, the compiler now knows that the `important` field should take priority when laying out the fields.
> If this is not done, it is possible that the important field is located at the end of the struct, causing more data needing to be loaded within memory to be able to acces this.
> But here the `important` field will be at the start of the type, requiring only the first cache line to be loaded to read the value.

## `C` representation [↵](#layout-representation)

The `C` representation is specified using the [`extern` specifier] on composite types, excluding on a tuple type.

This representation exists for 2 purposes:
- to allows interoperability with C code
- to be able to rely on the ordering of the fields

> _Warning_: primitive types may have different alignment when used in a type that uses the `C` representation.

### `extern struct` [↵](#c-representation-)

The alignment of an extern struct is that of the most-aligned field within the type.

The size and offset of fields is determined using the following algorithm.

With the current offset starting at 0, for each field:
1) determine the size and alignment of the field
2) if the current offset is not a multiple of the alignment, set the current offset to the next multiple of this alignment. This space is padding.
3) the offset of the field is set to the current offset
4) add the size of the field to the current offset

> _Example_:
> The algorithm can be described by the following pseudocode
> ```
> /// returns the amount of padding needed after `offset` to ensure that the following address will be aligned to `alignment`
> fn calc_required_padding(offset: uptr, alignment: uptr) -> uptr {
>     misalign := offset % padding;
>     if misalign > 0 {
>         // round up to the next alignment
>         alignment - offset
>     } else {
>         // already at the correct offset
>         0
>     }
> }
> 
> struct.alignment = struct.fields().map($0.alignment).max();
> 
> mut current_offset := 0;
> for field in struct.fields() {
>     // Ensure the offset is correctly aligned by adding the required padding to align the field correctly
>     current_offset += calc_required_padding(current_offset, field.alignment);
> 
>     struct[field].offset = current_offset;
> 
>     current_offset += field.size;
> }
> 
> struct.size = current_offset + calc_required_padding(current_offset, struct.alignment);
> ```
> 
> > _Warning_: The abovvee pseudocode naively calculated the layout and does not take into account any possible overflows.
> >            Whithin code, prefer using [`Layout`] to calculate any memory layout calculations

> _Note_: This algorithm may produce zero-sized structs.
>         While this is generally considered illegal in C, some compilers support an option to enable zero-sized structs.
>         C++ on the other hand, gives any empty structure a size of 1, unless they are are inherited from, or they have a field with the `[[no_unique_address]]` attribute,
>        in which case they do not contribute to the size of the overall struct.
> 
> To get around this, the [`@unique_addr` attribute] may be added to a type, indicating that this type will always be located at a unique address, and will as a result have a size of 1.

### `extern union` [↵](#c-representation-)

An extern union will have the same size and alignment as an equivalent union in C, as it would be defined for the target platform.

This means that the union will have a size of the field with the maximum size, rounded up to the next multiple of the aligment.
The alignment will be that of the field with the largest alignment.

In addition, all fields are guaranteed to have an offset of 0, relative to the start of the type.

_Example_
```
extern union Union {
    f1: u16,
    f2: [4]u8
}

assert(size_of(Union) == 4);
assert(align_of(Union) == 2);

extern union RoundedUp {
    f1: u32,
    f2: [6]u8,
}

// While the union only needs 6 bytes to store `f2`, it is rounded up to a size of 8, to adhere to the alignment
assert(size_of(RoundedUp) == 8);
// Alignment of `f1`
assert(align_of(RoundedUp) == 4);
```

### `extern enum` [↵](#c-representation-)

An extern enum is represented by an `extern struct` which includes 2 fields (also called a "tagged union"):
- a `C`-representation field with the type of the discriminant (the "tag")
- an extern unin containing extern structs, each with the field cotnained for the corresponding variant (the "payload")

> _Note_: Due to the `C` representation, if a variant has a single field, there is not difference between putting this field directly within the union, or wrapping it in a struct.
>         Any code wishing to manipulate such a representation, may therefore use whichever form is more convenient or consistent for it.

> _Example_
> ```
> extern enum Enum: u8 {
>     A(u32),
>     B(i32, f64),
>     C{ x: u32, y: u8 },
>     D,
> }
> ```
> has the same in-memory representation as
> ```
> extern struct EnumRepr {
>     tag: EnumDiscriminant,
>     payload: EnumFields
> }
> 
> extern enum EnumDiscriminant: u8 {
>     A, B, C, D,
> }
> 
> extern union EnumFields {
>     A: AFields,
>     B: BFields,
>     C: CFields,
>     D: DFields,
> }
> 
> @derive(Clone, Copy)
> extern struct A(u32);
> 
> @derive(Clone, Copy)
> extern struct B(i32, f64);
> 
> @derive(Clone, Copy)
> extern struct C {
>     x: u32,
>     y: u8
> }
> 
> // This type may be ommited, as it is a zero-sized type
> @derive(Clone, Copy)
> extern struct D;
> ```
> The enum could also be directly represented as a union, with each variant containing the corresponding tag field
> ```
> extern unin EnumRepr {
>     A: AFields,
>     B: BFields,
>     C: CFields,
>     D: DFields,
> }
> 
> extern enum EnumDiscriminant: u8 {
>     A, B, C, D,
> }
> 
> @derive(Clone, Copy)
> extern struct A(tag: EnumDiscriminant, u32);
> 
> @derive(Clone, Copy)
> extern struct B(tag: EnumDiscriminant, i32, f64);
> 
> @derive(Clone, Copy)
> extern struct A {
>     tag: EnumDiscriminant,
>     x:   u32,
>     y:   u8,
> }
> 
> EnumDiscriminant
> extern struct D(tag: EnumDiscriminant);
> ```

#### `extern` field-less enums and record enums, and flag enums

Fieldless enums have the same size and alignment as their disriminant type.

> _Note_ The enum representation in C is implementation defined, meaning that unless the discriminant is explicitly specified, a "best guess" is used to define the discriminant.
>        This may result in the discriminant's type not matching up with what is expected within `C`, which will lead to undefined behavior.
>        It is therefore always recommended to explicilty define the discriminant type on an extern enum.
> 
## `transparent` representation [↵](#layout-representation)

The transparent representation is only supported for structures and enums with only 1 variant, which adhere to the following:
- any number of non uniquely addressed 0-sized types and an alignment of 1
- at most 1 other field with a non-zero size

Types using this representation use the same layout as the single non-zero field, or it is a unit otherwise.

Because this layout inherits the layout of a different type, it cannot be used in conjunction of `extern`.



[`Layout`]:                    #extern-struct- "Todo: link to docs"
[`extern` specifier]:          ../type-layout.md#extern-
[`@unique_addr` attribute]:    ../../attributes/code-generation.md#unique_addr-
[`@field_priority` attribute]: ../../attributes/code-generation.md#field_prioity-
[`@transparent` attribute]:    ../../attributes/code-generation.md