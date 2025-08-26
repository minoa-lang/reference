# Layout representation

All user-defined composite types have a representation that specifies how the type is laid out.
The possible representations for these are:
- `Minoa`
- `C`
- `soa`
- primitive type
- `transparent`

While the representation of a type can affect the padding between fields, it does not change the layout of the fields themselves.
If a composite type contains a field that had another layout already defined, that field will still use its own layout representation, and will not use the layout representation of the type containing it.

## `minoa` representation [↵](#layout-representation)

The `minoa` represention is the default representation for nominal types without a `repr` attribute.
If this representation is explicitly specified by using the `repr` attribute, it will result in the same layout as if it was not explicitly defined.

This represetnation makes a mininal amount of guarantees about the layout of fields, but does guarantee the following:
- Each field is properly aligned
- Fields do not overlap
- The alignment of the type is at least that of teh field with the highest alignment.

The first guarante means that the offset of a field will always be a multiple of its alignment.
The second guarantee means that the fields can be ordered such that the offset plus the size of any field is less than or equal to the offset of the next field in the type.
This does not mean that zero-sized fields will have a unique offset and multiple zero-sized fields may be located at the same address.
The third guarantee ensures alignment of the all fields can always be guaranteed.

There is no guarantee that the ordering is the same as the one defined within code.

### Field priority [↵](#minoa-representation-)

Since by default there is no guarantee on the ordering of fields, the type may lay out fields in such a way that they may not be optimally laid out for some usecases.
To ensure the programmer can provide additional hints to the compiler which fields should be prioritized during layout to ensure better caching of the type,
a field priority propery may be defined per field.

The priority takes on a value in the range of 0..=15, with 0 being the default for all fields.
Fields with a higher priority will be prefered to be laid out first in the type.

```
struct Foo {
    big: [256]u8,
    // Ensure the compiler lays out the fields in such a way that important will be most likely to be on a cache line
    @[field_priority(15)]
    important: u32,
}
```

## C representation [↵](#layout-representation)

The C representation has 2 purposes:
- creating types that are interoperable with C libraries/code.
- allow types to be laid out in such a way that the layout of the type can be relied on.

This representation can be applied to `struct`s, `enum`s, and `union`s.

The C representation also affects the alignment of primitive types for the current target architecture.

### `repr(C)` structs and records [↵](#c-representation-)

The alignment of a struct will be that of the most-aligned field.

The size of the type, and the size and offset of the fields will be determined uisng the method described below.

The current offset start at 0, then for each field within a type:
1. determine the size and alignment of the field
2. if the current offset is not a multiple of the field's alignment, set the current offset to the next multiple of the field's alignment. This space is padding.
3. the current offset will now become the offset for the field
4. increment the current offset by the size of the field.

> _Note_: This algorithm can produce zero-sized structs.
> While this is generally considered to be illegal in C, some compiler support option to enable zero-sized structs.
> Meanwhile C++ gives empty structures a size of 1, unless the are inherited or have fields using the `[[no_unique_address]]` attribute,
> in which case they do not contribute to the size of the overall struct.

### `repr(C)` unions [↵](#c-representation-)

A union with a C representaton has the same layout as the union would have if it were defined in C for the target platform.

The union will have the size of the largest fields in the union, and the alignment of the most-aligned field in the union.
These values may be taken from different fields.

### `repr(C)` field-less enums and enum records, and flags enums [↵](#c-representation-)

When an enum is field-less, the C representation has the size and alignment of the default `enum` size for the target platform's C ABI.

> _Note_: The enum representation in C is implementation defined, so this is really a "best guess".
> In particular, this may be incorrect when the C code of interst is compile with certain flags
> If a known enum size is required, use a primitive represention.

### `repr(C)` enums and enum records with fields [↵](#c-representation-)

The representation of an enum with fields is defined a `repr(C)` structure with 2 fields, these being:
- a `repr(C)` version of the enum with all field removed, i.e. the "tag"
- a `repr(C)` union of `repr(C)` records for the field of each variant that had them, i.e. the `payload`

## Primitive representation

A primtiive representation is only allowed for `enum` values that have at least 1 variant, and on bitfields.

The allowed primitive types are `u8`, `u16`, `u32`, `u64`, `u128`, `usize`, `ui`, `i16`, `i32`, `i64`, `i128`, and `isize`.
When defining an enum with a primitive representation, an enum will use this type as its descriminant.

If an enum has no fields, the resulting enum will have the same size and aligment as the primitive type it is represented by.

When an enum has fields, it will be represented as a `repr(C)` enum, with its payload using the `repr(C)` representation.
In addition to the primitive representation, a second (non-primitive) representation may be provided, affecting the layout of the payload of the enum.

## Transparent representation [↵](#layout-representation)

The transparent representation is only supported for structures and enum with only 1 variant, which have the following:
- a single field with a non-zero size
- any number of field with a 0-sized type and alignment 1

Type using this representation have the same lyout and ABI as the single non-zero field.

Unlike other representations, a type with this represetnation takes on that of the underlying non-zero sized type.

## SAO (structure of array) representation [↵](#layout-representation)

> _Todo_

## Additional layout modifiers [↵](#layout-representation)

The `repr` attribute may also optionally contain an `align` or `packed` value, these can be used to raise or lower the alignment respectively.
On their own, neither provide guarantees about the ordering of any fields in the layout of the type, although they may be combined with representations such as 'C', which do provide such guarantees.

Either modifier may be applied to structs, unions, and records.
In addtion, `align` may also be applied to enums and enum records.

The alignment specified by either the `align` of `packed` attributes must be a power of 2 from 1 up to 2^32.
For `packed`, if no explicit alignment is given, this will default to 1.

Only one of the `align` or `packed` modifiers can be applied to a type at any type, and it may only be applied to types with either a `Minoa` or `C` representation.

#### `align`

The `align` modifier changes the minimum alignment for the type, if the value given is lower than the actual alignment of the type, the alignemnt is unaffected, otherwise, it will increase the alignment to the given value.

#### `packed`

The `packed` modifies affect the alignment of each fields within the type, but does not chang the alignment of the layout of the fields themselves.
If this alignment is larger than the alignment of the type, the offset of fields are unaffected.
Otherwise the offset of fields is affect, as this modifier affects the minimal required alignment of fields that is decided by the current representation, i.e. fields will be aligned to the alignment provided to the attribute.

> _Todo_: make clear that `packed` works on a byte boundary, and to use `bitfield` for packing in bits
