# Dynamically sized types

Most types have a fixed size that is known at compile time and implement the `Sized` trait.
A type with a size that is only known at run-time is called a dynamically sized type (DST), or informally, unsized types.
Slices and trait objects are two such examples.

DSTs can only be used in certain cases:
- Pointers and references to DSTs are sized, but have twice the size of a pointer of a sized type.
    - Pointers to slices store the number of elements in the slice.
    - Pointers to trait objects store a pointer to their vtable.
- DSTs can be provided as type arguments to generic type parameters that have a special `?Sized` bound.
  They can also be used for associated type definitions when the corresponding associated type is declared using the `?Sized` bound.
  By default, any type parameter has a `Sized` bound, unless explicitly relaxed using `?Sized`
- Trait may be implemented for DSTs.
  Unlike with generic type paramters, `Self is ?Sized` is the default in trait definitions.
- Struct may contains a DST as the last field, this makes the struct itself a DST.
  For example: a [final slice field]



[final slice field]: ./types/composite-types/struct-types.md#final-slice-fields-