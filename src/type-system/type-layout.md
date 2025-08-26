# Type layout

The layout of a type defines its size, alignment, and its internal representation of data/fields.
For enums, how their distriminant is laid out is also part of the layout.

Type layouts can change inbetween compilations.

## Size and alignment

All values have a size and alignment.

The alignment of a value specifies at what boundaries in memory the value can be stored.
A type with alignment `N` must be stored at an address that is a multiple of `N`.
Alignment is measured in bytes, is at least 1, and is a power of 2.

The size of a value specifies the offset that is needed to be able to place the next value, e.g. the offset of the subsequent element in an array.
The size will always be a multiple of its alignment, guruaranteeing that any subsequent value of this type will be correctly aligned by default.

Is it possible for a given type to be a zero-sized type, as a size of 0 is a valid multiple of its alignment.
On some platforms, a zero-sized types might still be required to follow a specific alignment, e.g. in the case of `[0]i32`, the value needs to be aligned to `4`.

the majority of types will know their size and alignment at compile time, these are called 'sized types'.
Sized types can have their size and alignment checked at compile time.
Meanwhile types that are not known at compile time, as known as [dynamically sized types](../dynamically-sized-types.md).

Since all values of a sized types share their size and alignment, we say that they have the type's size and alignment.

## Primitive layout

The size of most primitive types can be found in the table below:

Types                                                | Size/Alignment (bytes) | Size in bitfield (bits) | Alignment in bitfield (bits)
-----------------------------------------------------|------------------------|-------------------------|------------------------------
`i8`   / `u8`            / `b8`  / `char8`           | 1                      | 8                       | 8
`i16`  / `u16`  / `f16`  / `b16` / `char16`          | 2                      | 16                      | 16
`i32`  / `i32`  / `f32`  / `b32` / `char32` / `char` | 4                      | 32                      | 32
`i64`  / `i64`  / `f64`  / `b64`                     | 8                      | 64                      | 64
`f80`                                                | 10 / 2                 | 80                      | 16
`i128` / `u128` / `f128`                             | 16                     | 128                     | 128
`usize` / `isize`                                    | see below              | see below               | see below
`bool`                                               | 8                      | 1                       | 1
`char7`                                              | 1                      | 7                       | 1

`usize` and `isize` are different to other types, as they contain types that fit the entire memory address space of the target platform.
For example, on a 32-bit system, this is 4, and on an 64-bit system, this is 8.
These sized also often match up with that of the target register size, but this cannot be guaranteed.

The alignment of types is generally platform-specific, but to keep this consistent across architectures, Minoa has diced to make these the same as their size.

When used in a bitfield, some primitive types may have different sizes and alignment to fit more tightly into memory.

## Unit and never type layout

Unit and never types are both 0-sized types with an alignment of 1.

## Pointer and reference type

Pointers and references have the same layout.
The mutabilty of a pointer or reference has not impact on the layout.

Pointers and references to sized tyes are the same as those of a `usize`.

Pointers and references to usized types are typed. Their size and alignement is guaranteed to be at least eqal to the size of a `usize` and have the same alignment.

> _Note_: Currently all pointers and references to DST are twice the size of a `usize` and have the same alignment.
> Although this should not be relied on.

## Array layout

An array of the form `[N]T` has a size that is `N` times that of the size of type `T` and has the same alignment as type `T`.
Arrays are laid out so that the zero-based `n`th element of the array is offset from the start of the array by `n` times the size of type `T`.

When an array is sentinal terminated, the array contains an additional element of type `T` at the end, so the size of the array will be `N + 1` times the size of type `T`.

## Slice layout

Slices have the same alyout as a section of an array

> _Note_: This is about the ray `[]T` type, not pointers to arrays to slices, e.g. (`&[N]T`)

## String slice layout

A string slice's layout depends on the type of string slice, but they have the same representation as their internal slice layout.

Below is a table of string slices that have a corresponding type layout to the following slice types

String slice | Slice
-------------|-------
`str`        | `[u8]`
`str7`       | `[char7]`
`str8`       | `[char8]`
`str16`      | `[char16]`
`str32`      | `[char32]`
`cstr`       | `[char8]`

## Tuple layout

Tuples are laid out as defined in the [Minoa representation]().

## Trait object layout

Trait objects have the same layout as the value the trait that implements it.

> _Note_: THis is for the trait object itself, not a type containing the object, such as a reference.

## Closure layout

A closure has no layout guarantees.

## Bitfield layout

A bitfield will have the size and alignment of the smallest primitive types that fits the contents of the bitfield.
