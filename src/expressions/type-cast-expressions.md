# Type cast expressions
```
<type-cast-expr> := <expr> <as-op> <type>
<as-op> := 'as' | 'as?' | 'as!'
```

A type cast expression is a special binary operator, which has a type on the right hand side.

Executing the expression will cast the value on the left hand side to the type on the right hand side.

The cast comes in 3 forms:
- `as`: converts directly to a different type, expect all convertions between the types to be valid
- `as?`: tries to convert to a different type, return `.Ok(...)` when it succeeds, and an `.Err(...)` when it fails to
- `as!`: tries to convert to a different type, panics when it fails to


Each cast expression has traits associated with them:
cast  | from trait   | to trait 
------|--------------|-----------
`as`  | `From`       | `To`
`as?` | `TryFrom`    | `TryTo`
`as!` | `UnwrapFrom` | `UnwrapTo`

The from, to traits represent directional cast traits, and are as following:
- the from trait is implemented on the type being cast
- the to trait is impleented on the type being cast to

If either of these traits is implemented, the other will automatically be generated.

## Builtin casts [↵](#98-type-cast-expression--)

The builtin cast `as` can be used to explicitly perform coercions, as well as the following casts.
Any cast that does not fit either a coercion rule, an entry in the table below, or a user-defined conversion will generate a compiler error.
Here `^T` means either `^T` or `^mut T`. `m_` stands for an optional `mut` in reference and pointer types.

If a cast is set as bidirectional, it means that a value of `U` can also be cast to type `T`

Type of `e`                         | `U`                                 | Cast performed by `e as U`               
------------------------------------|-------------------------------------|------------------------------------------
Integer or Float type               | Integer or float type               | [Numeric cast]
Enumeration                         | Integer type                        | [Enum cast]
Boolean or character type           | Integer type                        | [Primitive to integer cast]
`u8`                                | Character type                      | [Integer to character cast]
`^T`                                | `^U` where `U` is sized *           | [Pointer to pointer cast] †
`^T` where `T` is sized             | Integer type                        | [Pointer to address cast] †
`&m1 T`                             | `^m2 T` **                          | [Reference to pointer cast] †
`&m1 [N]T`/`^m1 [N]T`               | `^m2 T`/`[^]m2 T` **                | [Array to pointer cast] †
`&m1 [N]T`/`^m1 [N]T`               | `^[]T` **                           | [Array to slice pointer cast] †
`^m1 []T`/`^m1 []T`                 | `^m2 T`/`[^]m2 T` **                | [Array to pointer cast] †
Function item                       | Function pointer                    | [Function item to function pointer cast] †
Function item                       | `^U` where `U` is a supported type  | [Function item to pointer cast] †
Function item                       | Integer                             | [Function item to address cast] †
Function pointer                    | `^U` where `U` is a supported type  | [Function pointer to pointer cast] †
`^T` where `T`  is a supported type | Function pointer                    | [Pointer to function pointer cast] 
Function pointer                    | Integer                             | [Function pointer to address cast] †
Closure ***                         | Function pointer                    | [Closure to function pointer cast] †
`T`                                 | Opaque type with same size as `T`   | [Type to opaque cast]
`^T`                                | `^U` where `U` is an opaque type    | [Type to opaque cast]
`&m1 T`                             | `&m2 U` where `U` is an opaque type | [Type to opaque cast]
Opaque type with same size as `U`   | `U`                                 | [Opaque to type cast] †
`^T` where 'U' is an opaque type    | `^U`                                | [Opaque to type cast] †
`&m1 T` where 'U' is an opaque type | `&m2 U`                             | [Opaque to type cast] †

\* or `T` and `U` are compatible unsized types, e.g. both slices, both are the same interface 

\** only when `m1` is `mut` or `m2` is `const`. Casting `mut` reference to `const` pointer is allowed.

\*** only for closure that do not capture (close over) any local variables

† Casts are unsafe

> _Note_: casting an integer type to a pointer is only allowed via the appropriate library functions

### Numeric cast semantics [↵](#981-builtin-casts-)
- Casting between two integer types of he same size (e.g. i32 -> u32) is a no-op (2's complement is used for negative numbers)
- Casting from a larger integer to a smaller integer (e.g. u32 -> u8) will truncate
- Casting from a smaller integer to a larger integer (e.g. u8 -> u32) will
  - Zero extend when the source is unsigned
  - Sign extend when the source is signed
- Casting from a float to an integer will round the float towards zero
  - `NaN` will return 0
  - Values larger than the maximum value, including `INFINITY`, will saturate to the maximum value of the integer type
  - Values samller than the minimum integer value, including `-INFINITY`, will saturate to the minimum value of the integer type
- Casting from an integer to a floating point will produce the closest possible float *
  - if necessary, rounding is according to the currently set [rounding mode]
  - on overflow, infinity (of the same sign as the input) is produced
  - note: with the current set of numeric types, overflow can only happen on `u128 as f32` for values greater or equal to `f32::MAX + 0.5`, for for casts to an `f16`
- Casting from an f32 to an f64 is perfect and lossless
- Casting from an f64 to an f32 will produce the closest possible f32 value **
  - if necessary, rounding according to `roundTiesToEven` mode ***
  - on overflow, infinity (of the same sign as the input) is produced

\* if integer-to-float casts with this rounding mode and overflow behavior are not supported natively by the hardware, these casts will likely be slower than expected.

\** If f64-to-f32 casts with this rounding mode and overflow behavior are not supported natively by the hardware, these casts will likely be slower than expected.

### Enum cast semantics [↵](#981-builtin-casts-)

Casts from an enum to its distriminant, then uses a numeric cast if needed. Casting is limited to the following kinds of enumerations:
- Unit-only enums
- Field-less enums without explicit discriminants, or where only unit variants have explicit discriminants
- Flag enums

### Primtive to integer cast semantics [↵](#981-builtin-casts-)

- `false` casts to 0, `true` casts to 1.
- character types cast to the value of the code point, then uses a numeric cast if needed.

### Integer to character cast semantics [↵](#981-builtin-casts-)

Casts a `u8` corresponding to a character type, then cast to a character type with the corresponding codepoint.
To convert from larger integers, use `as?`, `as!`, or any of the provided functions.

### Pointer to address cast semantics [↵](#981-builtin-casts-)

Casting from a raw pointer to an integer produces the machine address of the referenced memory.
If the integer type is smaller than the pointer type, the address may be truncated; using `usize` avoids this.

### Pointer to pointer cast semantics [↵](#981-builtin-casts-)

`^T`/`^mut T` can be cast to `^U`/`^mut U` with the following behavior:
- If `T` and `U` are both sized, the pointer returned is unchanged.
- If `T` and `U` are both unsized, the pointer is also returned unchanged. In particular, the metadata is preserved exactly.
  If `T` and `U` are objects, this does require that they are compatible types, e.g. same non-marker interfaces.

  For instance, a cast from `^[]T` to `^[]U` preserves the number of elements.
  Note that, as a consequence, such casts do not neccesarily preserve the size of the pointer's reference (e.g. casting `^[u16]` to `^[u8]`) will result in a raw pointer which refers to an object of half the size of the original.
  The same holds for `str` and any compound type whose unsized tail is a slice type, such as `struct Foo(i32, [u8])` or `(u64, Foo)`
- If `T` is unsized and `U` is sized, the cast discards all metadata that completes the wide pointer `T` and produces a thin ponter `U` consisting of the data part of the unsized pointer.

### Reference to pointer casts [↵](#981-builtin-casts-)

References may be directly cast to a pointer, but this loses compiler information about the reference's capture status.
Because of this, the reverse of this cast requires a pointer re-borrow.

### Array and slice to pointer casts [↵](#981-builtin-casts-)

Arrays me be cast to a slice pointer, this will be equal to the following: `&arr[..] as ^[]T`, resulting in a pointer to the slice with the lenght of the array.

When an array or a slice are cast from a reference or pointer, the following semantics are used:
- if cast to a single element pointer, the pointer will point to the first element and only that element will be available
- if cast to a multi element pointer, the pointer will point to the first element, with the other values accessible from there
  It is up to the user to ensure the array or slice is not accessed out of bounds.

### Function cast semantics [↵](#981-builtin-casts-)

Function items can be cast to a function pointer, and have the same semantics as function pointers for other casts.

When casting from function items, or to and from function pointers, they require that the non-function pointer that is being cast to or from is one of the following:
- A trait object to one of the following:
    - `Fn`
    - `FnMut`
    - `FnOnce`
- A unit type

Non-capturing closures can be cast to function pointers with a matching signature.

> _Todo_: Check if the function traits mentioned are correct

### Type to opaque cast semantics [↵](#981-builtin-casts-)

Any type may be cast to its corresponding opaque type, meaning that:
- `T` can directly be cast to a size opaque type of the same type
- `^T`/`^mut T` can directly be cast to a `^U`/`^mut U`, where `U` is either an unsized, or same sized opaque type
- `&T`/`&mut T` can directly be cast to a `&U`/`^mut U`, where `U` is either an unsized, or same sized opaque type

`T` may either be a sized or unsized type when casting from pointers or references.

### Opaque to type cast semantics [↵](#981-builtin-casts-)

Any opaque type may be cast to its corresponding non-opaque type, meaning that:
- `T` can be directly cast to a type `U` that is sized
- `^T`/`^mut T` can directly be cast to a `^U`/`^mut U`, where `T` is either an unsized type, or same sized opaque type as `U`
- `&T`/`&mut T` can directly be cast to a `&U`/`&mut U`, where `T` is either an unsized type, or same sized opaque type as `U`

> _Note_: Since there is not guarantee that the underlying data in the opaque type is valid, this is an `unsafe` operation

## Try and unwrap casts [↵](#type-cast-expressions)

A try cast `as?` can be used to cast a type from an interface object, impl interface object, or a generic to a given type, returning an optional type with a valid value when the cast is possible and a `None` when it's not.
This can therefore be used to dynamically handle code based on a type when RTTI info is available.

An unwrap cast `as!` is similar to a try cast, but meant for in usecases the user is certain that the cast is possible, as it will unwrap the resulting nullable type.
This could also be written as `(a as? T)!`.
By default, it will panic when the cast is not available, but in certain configuration, this can be changed into a cast that always passes, so may return in UB if not used correctly.

Any cast that happens on a generic or impl interface object will be resolved at compile time.



[Array to pointer cast]:                  #array-and-slice-to-pointer-casts-
[Array to slice pointer cast]:            #array-and-slice-to-pointer-casts-
[Array to pointer cast]:                  #array-and-slice-to-pointer-casts-
[Enum cast]:                              #enum-cast-semantics-
[Function item to pointer cast]:          #function-cast-semantics-
[Function item to address cast]:          #function-cast-semantics-
[Function pointer to pointer cast]:       #function-cast-semantics-
[Pointer to function pointer cast]:       #function-cast-semantics-
[Function pointer to address cast]:       #function-cast-semantics-
[Closure to function pointer cast]:       #function-cast-semantics-
[Function item to function pointer cast]: #function-item-casts-semantics-
[Integer to character cast]:              #integer-to-character-cast-semantics-
[Numeric cast]:                           #numeric-cast-semantics-
[Reference to pointer cast]:              #reference-to-pointer-casts-
[Type to opaque cast]:                    #type-to-opaque-cast-semantics-
[Opaque to type cast]:                    #opaque-to-type-cast-semantics-
[Primitive to integer cast]:              #primtive-to-integer-cast-semantics-
[Pointer to pointer cast]:                #pointer-to-pointer-semantics-
[Pointer to address cast]:                #pointer-to-address-cast-semantics-
[rounding mode]:                          ../attributes.md#rounding-