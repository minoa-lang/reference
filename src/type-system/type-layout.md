# Type layout

The layout of a type defines how a type is laid out within memory.
This constits of its size, alignment, and offset of any fields.
For enums, this also includes how their descriminant is defined and stored.

Since type layout can change inbetween subsequent compilations, this section only defines what is guaranteed for a type.

> _Note_: Different types with the same layout may still differ on how they are passed across functions.
>         More info can be bound within the section about ABI compatibility of types for function, defined [here](#type-layout "Todo: fix link")

## Size and alignment [↵](#type-layout)

All values have a size and alignment.

The alignment is avalue which specifies at what boundary in memory a value can be stored.
A type with an alignment `N` must be stoed at an address which is a multiple of `N`.
The alignment of yptes follow the following basic rules:
- alignment is a non-zero power of 2
- each type has an alignment which is at least the alignment of the field with the greatest alignment

The alignment of a type can be gotten using the [`align_of`] function for types, and the [`align_of(val:)`] function for a value of a given type.

> _Note_: Alignment may be explicitly specified using the `align(N)` specifier.

> _Todo_: Add this specifier to types

The size of a type is the offset to the next instance of the type when they are stored within sequential memory, which includes alignment padding.
Since the size has to include padding for alingment, it result that any type will have a size which is a multiple of its alignment.

The size of a type can be gotten using the [`size_of`] function for types, and the [`size_of(val:)`] function for a value of a given type.

> _Note_: Some types may have a 0-size, as 0 is considered as a multiple of the alignment.
>         e.g. `[0]u16` is a type which can have a 0 size, but a 2-byte alignment.

The majority of types will have a size and alignment which is known at compile time.
These types implement the [`Sized`] trait.

Type that do not implement this trait are [dynamically sized types].

Since all values of a sized type share their size and alignment, we say that they have the type's size and alignment.

## Layout specifiers [↵](#type-layout)
```
<type-layout-specifiers> := [ <extern-specifier> ] [ <align-specifier> ] [ <packed-specifier> ]
```

### `extern` [↵](#layout-specifiers-)
```
<extern-specifier> := 'extern'
```

The extern specifier ensures a type has a `C`-like layout.

### `align` [↵](#layout-specifiers-)
```
<align-specifiers> := 'align' '(' <expr> ')'
```

The alignment specifier can be used to define the alignment of a given type.
This value is provided by a compile-time constant, and must be a non-zero power of 2.

If the alignment provided to the specifier is less than the alignment would have been without this specifier, then the specifier is ignored.
Meaning that this specifier may only increase the minimum alignment of a type, and not decrease it.

> _Note_: This does not affect the layout within the type, as all fields are still aligned relative to the start of the type using their own alignment.

> _Note_: the maximum supported alignment is implementation defined and can be retrieves by the [`MAX_ALIGNMENT`].



This specifiers not only allows a type to be given an alignment which is greater than that the type would have by default, but also allows a type to have a smaller alignment that its fields.
When a pointer is taken to a field which would require a greater alignment than the type, this pointer will have the same alignment as the containing type.
References cannot be taken to a field, if its required alighment is greater than that of the type.

### `packed` [↵](#layout-specifiers-)
```
<packed-specifier> := 'packed' [ '(' <expr> ')' ]
```

The packed specifier affexts the alignment of each field within the type, but does not affect aligment inside the fields themselves.
The specifier may be supplied by the packing aligment used, this must be a non-zero power of 2, if none is supplied, this will default to 1.

If a field has an alignment that is smaller or equal to this packing alignment, the field in unaffected.
If the field's alignment is larger, it will not be laid out as if it only had the alighment provided by the packed attribute.

> _Note_: The `packed` specifier works on a byte boundary level, for packing values into bits, use a [bitfield]

> _Example_:
> ```
> extern struct Foo {
>     a: u8,
>     c: u32,
>     b: u16,
> } 
> ```
> this would layout this type as following
> ```
>   0   1   2   3   4   5   6   7   8   9
> +---+---+---+---+---+---+---+---+---+---+
> | a |    pad    |       b       |   c   |
> +---+---+---+---+---+---+---+---+---+---+
> ```
> and requires 10 bytes,
> 
> However, if we marked this as `packed(2)`, the alignment restrictions on `b` will be relaxed, allowing it to be located at an offset with a smaller alignment, i.e.
> ```
> extern packed(2) struct Foo {
>     a: u8,
>     c: u32,
>     b: u16,
> }
> ```
> this would instead be laid out as
> ```
>   0   1   2   3   4   5   6   7
> +---+---+---+---+---+---+---+---+
> | a |pad|       b       |   c   |
> +---+---+---+---+---+---+---+---+
> ```
> and require only 8 bytes
>
> > _Note_: This also allows for more compact non-`extern` types, but this was used to show an example with a known field order,
> > as otherwise in the initial representation, `c` might have been placed within the padding between `a` and `b`.

## Primitive layout [↵](#type-layout)

The size and alignment of primitive types is generally defined by the bit-width in the type, e.g. a `u32` takes up 32-bits within a bitfield, and 4 bytes outside of one.
This size is also the alignment of the type.

There are however some types that either don't have their size defined within the type, or work slightly differently, these are the following:
- `bool` is a 1-bit type, or 1-byte outside of a bitfield
- `char` is a 32-bit type
- `f80` takes in 80-bits (10 bytes), but is aligned to 2 bytes
- `usize` and `isize` depend on the native register size of an architecture, e.g. 4 bytes on a 32-bit system, and 8-bytes on a 64-bit system
- `uptr` and `iptr` depend on the native address register size of an architecture, this generally matches that of a `usize` or `isize`, but may differ

> _Note_: In some language, the alignment of even an `i64` type may only havee a 4-byte alignment, to keep this consistent in `minoa`, they will always match the types size

## Unit and never type layout [↵](#type-layout)

Unit and never types are both 0-sized types with an alignment of 1.

## Pointer and reference layout [↵](#type-layout)

Both pointer and reference types have the same size and alignment as a `uptr` type.

When pointing to [dynamically sized types], they keep the same alignment of a `uptr`, and are at least equal to the size of one. 

> _Note_: The alignment of a pointer type is independent of the alignment specified with the pointer type, as it points to the alignment of the type being pointed to, and not that of the actual pointer.

> _Note_: Although this should not be relied upon, a pointer or reference to a DST are generally twice the size of a `uptr`

## Array layout [↵](#type-layout)

An array of the form `[N]T` has a size that is `N` times the size of type `T`, and has the same alignment as type `T`.
Arrays are laid out such that the zero-based `n`th element of the array is at on offset equal to `n` times the size of type `T`.

When an array is sentinel terminated, it contains an additional element of of type `T` past the end of the array, resulting to the actual in-memory representation of the array being `N + 1` times the size of type `T`.

## Slice layout [↵](#type-layout)

A slice has the same layout as that of an array type.

> _Note_: This is the the size of a raw `[]T` type, not of a type pointing to a slice.
>         A pointer-like type to a slice (`^[]T`) has the same size as one pointing to an unsized types.
>         This layout should not be relied on.

## String array and slice layout [↵](#type-layout)

A string array has the same layout as an array of its corresponding element type.
Similarly for a string slice, which has the same layout of a slice of its corresponding element type.

The underlying element types are defined in the encoding section located [here](./types/sequence-types/string-array-slice-types.md)

## Tuple layout [↵](#type-layout)

A tuple is laid out as defined in the [`minoa` representation].

## trait object layout

Trait objects have the same layout that a value of the type implmementing the trait has.

> _Note_: THis is for the raw trait boject tpye, not of a type pointing to a pointing to it.

## Closure layout

A closure has no layout guarantees.



[`align_of`]:              #size-and-alignment- "Todo: link to docs"
[`align_of(val:)`]:        #size-and-alignment- "Todo: link to docs"
[`Sized`]:                 #size-and-alignment- "Todo: link to docs"
[`size_of`]:               #size-and-alignment- "Todo: link to docs"
[`size_of(val:)`]:         #size-and-alignment- "Todo: link to docs"
[`MAX_ALIGNMENT`]:         #size-and-alignment- "Todo: link to docs"
[dynamically sized types]: ./dynamically-sized-types.md
[bitfield]:                ./types/composite-types/bitfield-types.md
[`minoa` representation]:  ./type-layout/composite-layout.md#minoa-representation-