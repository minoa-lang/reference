# Type cast expressions
```
<type-cast-expr> := <expr> <as-op> <type>
<as-op>          := `as` | `as?` | `as!`
```

A type cast expression can be seen as a special version of an infix operator, with a type as its right-hand operand.
The expression will cast the value of the left-hand operand to the type that is defined by the right-hand operand.

The cast comes in the following forms:
- `as`: converts directly to the given type, this cast is guaranteed to succeed
- `as?`: tries to convert to the given type, returns `.Ok(...)` if it succeeds, or `.Err(...)` if it fails to convert
- `as!`: converts to the given type, but will panic when the type cannot be converted

The latter 2 casts are also known as _fallible casts_.

Each version has 2 traits associated with them, one implemented by the type being converted from, and one on the type being converted to.
Only one of these traits may be manually implemented, the compiler will ensure this and will generate an error in case this happens.
The other corresponding trait will be automatically generated.

The following traits corresponds to each form:

cast  | from trait   | to trait
------|--------------|----------
`as`  | `From`       | `To`
`as?` | `TryFrom`    | `TryTo`
`as!` | `UnwrapFrom` | `UnwrapTo`

> _Note_: If the resulting type can be derived from the surrounding context, a cast can be written as `e as _`

> _Note_: To reinterpret the the data of a type as another type, the [`transmute`] function can be used

> _Note_: The `core` library also adds a couple generic implementations, allowing for the following casts, if a cast from `T` to `U` is implemented:
> - `?T` -> `?U`
> - `Result<T, E>` -> `Result<U, E>` (i.e. `E!T` -> `E!U`)

Each cast can also define additional restriction, see their relavent section for more info.
`as?` and `as!` should always be expected to have at minimum some overhead, regardless of how the overhead is defined within this file.

Below, each cast also has a one of the following values defined in their respective 'overhead guarantees' column:
- `none`: no guarantees about its overhead
- `zero`: always zero-overhead
- `minimal`: close to zero-overhead, usually a direct call to a machine instruction (when supported by hardware), their semantics may additionally define certain casts as zero-overhead
- `depends`: depends on the exact semantics being used, see their semantics section for more details.

## Fallible casts

As mentioned above, the `as?` and `as!` casts can fail if a type cannot be converted.

A _try cast_ `as?` can is used in cases where there is no guarantee a type will be able to be coverted correctly, and is allowed to return an error to indicate this.
An example usecase would be upcasting from a pointer to a [trait object type] to one to a concrete type, but thiss does require type info to be available.

A _unwrap cast_ `as!` is similar to a try cast, except that it will either panic on failure, or resolve it in a library specified way for its types.
The user must therefore ensure that the behavior of this casts has the behavior they expect, as it may behave as an 'unwrap or' expression.
The default implementation of this casts is equivalent to `(e as? U)!`.

## Builtin casts [↵](#type-cast-expressions)

The direct casts comes with a set of predefined casts (either provided directly by the compiler or via the `core` library), as well as allowing explicit [coercions].
Any cast that does not fit either a coercion rule, one of the built-in casts defined below, or a user-defined conversion, will result in an error.

The following coercions are builtin for the form `e as U`, `e as? U`, and `e as! U`.
Where `e` is the source value and `U` is the type being casted to.
Here, `^T` represents any pointer type (additional limiations are defined in each section).
In addition, an `m_` representd an optional `mut` for certain types, if the source type does not have a `mut` value, the type being casted to may also not contain it.

Type of `e`                                        | `U`                                                | Cast performed                        | overhead guarantees
---------------------------------------------------|----------------------------------------------------|---------------------------------------|---------------------
[integer] or [float]                               | [integer] or [float]                               | [numeric cast]                        | mininal
[enum]                                             | discriminant type                                  | [enum cast]                           | zero
[boolean] or [character]                           | [integer] or [float]                               | [primitive to numeric cast]           | mininal
[integer]                                          | [character]                                        | [integer to character cast]           | zero
`^T`                                               | `uptr`                                             | [pointer to address cast]             | zero
`^T`                                               | `^U`                                               | [pointer to pointer cast] †           | depends
`&m1 T`                                            | `^m2 T`                                            | [reference to pointer cast] †         | zero
`&[N]T` / `^[N]T`                                  | `^T` / `[^]T`                                      | [array to pointer cast] †             | zero
`&[]T` / `^[]T`                                    | `^T` / `[^]T`                                      | [array to pointer cast] †             | zero
`&[N]T` / `^[N]T`                                  | `&[]T` / `^[]`                                     | [array to slice pointer cast] †       | minimal
reference or pointer to a [function item]          | reference or pointer to a [raw function type]      | [function to raw function cast] †     | zero
reference or pointer to a [function item]          | `&dyn U` / `^dyn U`, where `U` is a function trait | [function to function trait cast] †   | minimal
reference or pointer to a non-capturing [closures] | reference or pointer to a [raw function type]      | [closure to raw function cast] †      | minimal
reference or pointer to a [closure]                | `&dyn U` / `^dyn U`, where `U` is a function trait | [closure to function trait cast] †    | minimal

† Casts are `unsafe`

### Numeric cast semantics [↵](#built-in-casts-)

Numeric casting generally works the same regardless of the casts used, but there are some execptions, these will be explicitly mentioned when they occur.
If not mentioned, the additional limitation for `as?` and `as!` is that the value being convert from must fit within the numeric type being casted to.

Numeric casting has the following semantics:
- casting between 2 integer types of the same size (e.g. `i32` -> `u32`) is a no-op (2's complement is used for negative numbers)

  ```
  assert(32i8 as u8 == 42u8);
  assert(-1i8 as u8 == 255u8);
  assert(255u8 as i8 == -1i8);
  assert(-1i16 as u16 == 65535u16);
  
  assert(-1i8 as? u8 == null);
  
  // will panic
  // -1i8 as! u8;
  ```
- casting from a larget integer to a smaller integer (e.g. `i32` -> `i8`) will truncate
  ```
  assert(42u16 as u8 == 42u8);
  assert(1234u16 as u8 == 210u8);
  assert(0xabcdu16 as u8 == 0xabu8);
  
  assert(42i16 as i8 == -42i8);
  assert(1234u16 as i8 == -46i8);
  assert(0xabcdu16 as i8 == -51i8);
  
  assert(1234u16 as? u8 == null);
  
  // will panic
  // 1234u16 as! u8;
  ```
- casting from a smaller integer to a larger integer (e.g. `i8` -> `i32`) will
  - zero extend if the source is unsigned
  - sign extended if the source is signed
  ```
  assert(42i8 as i16 == 42i16);
  assert(-17i8 as i16 == -17i16);
  assert(0b1000_1010u8 as u16 == 0b0000_0000_1000_1010u16); // zero-ext
  assert(0b0000_1010u8 as u16 == 0b0000_0000_0000_1010u16); // sign-ext 0
  assert(0b1000_1010i8 as u16 == 0b1111_1111_1000_1010u16); // sign-ext 1
  ```
- casting from float to integer will round the float towards zero, with the following exceptions (for `as?` and `as!`, these will fail to convert)
  - `NaN` is converted to 0
  - values larger than the maximum value integer value, including `+Inf` will saturate to the maximum value of the integer
  - values smaller tha nte minimum value integer value, inslucing `-Inf`, will saturate to the minimum value of the integer
  ```
  assert(42.7f32 as i32, 42i32);
  assert(-42.7f32 as i32, -42i32);
  assert(42_000_000f32 as i32 = 42_000_000i32);
  assert(f32.NAN as i32 == 0);
  assert(1_000_000_000_000_000f32 as i32 = 0x7FFF_FFFFi32);
  assert(-f32.INFINITY as i32 = -0x8000_0000i32);
  
  assert(f32.NAN as? i32 == null);
  assert(f32.INFINITY as? i32 == null);
  
  // will panic
  // f32.NAN as! i32;
  // f32.INFINITY as! i32;
  ```
- casting from an integer to a float will produce the closest possible float *
  - if neccesary, rounding is done according to the current [rounding mode].
  - on overflow, `Inf` (of the same sign as the input) is produced. Both `as?` and `as!` will fail to convert
  - currently only casts from `u128` to `f32`, or cast to `u16` can cause an overflow
  ```
  assert(1337i32 as f32 == 1336f32);
  assert(123_456_789i32 as f32 == 123_456_792f32); // rounded
  assert(u128.MAX as f32 == f32.INFINITY);
  
  assert(u32.MAX as? f16 == null);
  
  // will panic
  // u128.MAX as f32;
  ```
- casting from a smaller float to a larger float will always be lossless
  ```
  assert(1234.5f32 as f64 == 1234.5f64);
  assert(f32.INFINITY as f64 == f64.INFINITY);
  assert((f32.NAN as f64).is_nan());
  ```
- casting from a larger float to a smalle float will produce the closest possible value **
  - if neccesary, rounding occurs according to the current [rounding mode]
  - on overflow, `Inf` (of the same sign as the input) is produced. Both `as?` and `as!` will fail to convert
  ```
  assert(1234.5f64 as f32 == 1234.5f32);
  assert(1234567891.234f64 as f32 == 1234567892f32); // rounded
  assert(f64.INFINITY as f32 == f32.INFINITY);
  assert((f64.NAN as f32).is_nan());
  
  assert(f64.INFINITY as? f32 == null);
  
  // will panic
  // f64.INFINITY as f32;
  ```
  
\* if integer-to-float casts with the current rounding mode or overflow behavior are not supported by hardware, this operation will be slower

** if larger float to smalle float casts with the current rounding mode or overflow behavior are not supported by hardware, this operation will be slower

### Enum cast semantics [↵](#built-in-casts-)

Casts from an [enum] to its discriminant type.
Casting is limited to the following enums:
- Unit-only enums
  ```
  enum Enum enum Enum { A, B, C }
  
  assert(Enum.A as usize == 0);
  assert(Enum.B as usize == 1);
  assert(Enum.C as usize == 2);
  ```
- [fieldless enums] without [assigned discriminant values]
  ```
  enum Enum {
      A,
      B(),
      C{},
  }
  
  assert(Enum.A as usize == 0);
  assert(Enum.B() as usize == 1);
  assert(Enum.C{} as usize == 2);
  ```
- [fieldless enums] with an [explicit discriminant] and [assigned discriminant values]
  ```
  enum Enum: i32 {
      A = 4,
      B(),
      C{} = 7,
  }
  
  assert(Enum.A as usize == 4);
  assert(Enum.B() as usize == 5);
  assert(Enum.C{} as usize == 7);
  ```
- [flag enums]
  ```
  flag Enum {
      A,
      B,
      C
  }
  
  assert(Enum.A as usize == 0x1);
  assert(Enum.B as usize == 0x2);
  assert(Enum.C as usize == 0x4);
  ```

### Primitive to numeric semantics [↵](#built-in-casts-)

Certain non-numeric builtin types can be converted to numeric types (integers and floats).

Casts to integers have zero-overhead , unless cast to a diffently sized types.
All ohter casts will have minimal overhead, but are not guaranteed to be zero-overhead

- booleans: `false` casts to `0` and `true` casts to `1`
  ```
  assert(false as i8 == 0);
  assert(true as i8 == 1);

  assert(true as f32 == 1.0);
  ```
- characters: casts to the value of the code-point, casting to an integer with a smaller bitwidth will truncate its value
  - `as?` and `as!` will fail to convert if the codepoint does not fit within its type
  ```
  assert('A' as i32 == 65);
  assert('國' as i32 == 0x570B);

  assert('A' as f32 = 65.0);
  
  assert('A' as i8 == 65);
  assert('國' as i8 == 0x0B);
  
  assert('國' as? i8 == null);
  
  // will panic
  // '國' as! i8;
  ```

### Integer to character semantics [↵](#built-in-casts-)

Integers can be converted to a character with the same codepoint.
A direct casts does not guarantee that the character type will contain a value codepoint, direct casts are therefore limited to only cast from `u8` to `char`, `char16`, and `char32`.

All other casts must be done using fallible casts.

```
assert(65 as char == 'A');

// error: cannot convert directly from `u32` to `char`
// assert(0x570Bu32 as char == '國');

assert(0x570B as? char == '國');

// surrogate
assert(0xD800 as? char == null)

// will panic
// 0xD800 as! char;
```

### Pointer to address semantics [↵](#built-in-casts-)

Casting from a raw pointer to a `uptr` produces the machine address of the referenced memory.
This is the only direct cast allowed, unlike casting from a character, as there is not as great of a use of obtaining only a partial address.

This cast removed all metadata that is stored in the pointer, so the reverse cast is no allowed, and must be done via the supplied [address functions].

```
a_val := 42;

a: ^i32 = &raw a_val;

// cast
_ = a as uptr;
```

### Pointer to pointer semantics [↵](#built-in-casts-)

Pointer can be cast to different pointer types, as long as they have compatible pointer kinds, specifiers and metadata.
The overhead is defined by the associated metadata of `T` and `U`.

Converting between pointer kinds is only allowed in the following directions:
- `^T` -> `^U`
  ```
  a_val := 32;
  
  a: ^a = &raw a_val;
  
  // allowed cast
  _ = a as ^u32;
  
  // error: cannot cast from a single element pointer to a multi-element pointer
  // _ = a as [^]i32;
  
  // error: cannot cast from a single element pointer to a sentinel-terminated multi-element pointer
  // _ = a as [^:0]i32;
  ```
- `[^]T` -> `[^]U` and `^U`
  ```
  a_val := [1, 2, 3];
  
  a: [^]i32 = &raw a_val;
  
  // allowed casts
  _ = a as ^i32;
  _ = a as [^]u32;
  
  // error: cannot cast form a mult-element pointer to a sentinel terminated multi-element-pointer
  // _ = a as [^:0] i32
  
  ```
- `[^:N]T` -> `[^:N]U`, `[^]U`, and `^U`
  ```
  a_val := [1, 2, 3, 0];
  
  a: [^:0]i32 = &raw a_val;
  
  // allowed casts
  _ = a as ^i32;
  _ = a as [^]i32;
  _ = a as [^:0]u32;
  
  // error: cannot cast between sentinel terminated multi-element pointes with different sentinel values
  // _ = a as [^:3];
  ```

Converting from specifiers is only allowed in the following directions:
- mutability:
  - non-`mut` -> non-`mut`
  - `mut` -> `mut` and non-`mut`
  ```
  a_val := 42;
  mut b_val: 1337;
  
  a: ^i32 = &raw a_val;
  b: ^mut i32 = &raw b_val;
  
  // allowed casts
  _ = a as ^i8;
  _ = b as ^mut i8;
  _ = b as ^i8;
  
  // error: cannot cast from an immutable to a mutable pointer
  // _ = a as ^mut i8;
  ```
- [`volatile`]:
  - `volatile` -> `volatile`
  - non-`volatile` -> `volatile` and non-`volatile`
  ```
  a_val := 42;
  
  a: ^i32 = &raw a_val;
  b: ^volatile = 0xDEADBEEF;
  
  // allowed casts
  _ = a as ^i32;
  _ = a as ^volatile i32;
  _ = b as ^volatile i32;
  
  // error: cannot cast from a volatile to a non-volatile pointer
  // _ = b as ^i32;
  ```
- [`allowzero`]:
  - `allowzero` -> `allowzero`
  - non-`allowzero` -> `allowzero` and non-`allowzero`
  ```
  a_val := 42;
  
  a: ^i32 = &raw a_val;
  b: ^allowzero i32 = null;
  
  // allowed casts
  _ = a as ^i32;
  _ = a as ^allowzero i32;
  _ = b as ^allowzero i32;
  
  // error: cannot cast from an allowzero to a non-allowezero pointer
  // _ = b as ^i32;
  ```
- [alignment]:
  - `align(N)` -> `align(M)`, where `N >= M`
  - `align(N)` -> `align(M)`, where `N < M`, only using `as?` and `as!`.
    The cast will fail if the address is not sufficiently aligned for the `align(M)` pointer.
  ```
  a_val := 32;
  
  a: ^align(2) i32 = &raw a_val;
  b: ^align(4) i32 = &raw a_val;
  
  // allowed casts
  _ = a as ^align(2) i32;
  _ = b as ^align(2) i32;
  _ = b as ^align(4) i32;
  
  // error: cannot cast to a pointer with a larger alignment
  // _ = a as ^align(4) i32;
  
  // instead, the following is allowed
  c := a as? ^align(4) i32;
  assert(c == null);
  ```


The following compatible metadata is allowed, and can affect the value of the resulting pointer:
- both `T` and `U` are `Sized`, or either/both have a [final slice field]. The pointer will remain unchanged.
  It is up to the user to ensure the cast will result in valid data for the resulting type.
  In case of a final slice field, any data stored in the type to keep track of the size of the final slice field may contain a value that goes out of range of the data stored in it.

  This cast has zero-overhead.

  ```
  a_val := 42;
  
  a: ^i32 = &raw a_val;
  
  // cast
  _ = a as ^u32;
  ```
- `T` is sized, and `U` is a [trait object type] that is compatible with the implementation of `T`.
  The pointer will contain the original pointer as the pointer to data, and will, at compile-time, create the vtable which is assigned upon casting.

  This cast has minimal overhead.
- both `T` and `U` are [trait object types], the pointer will depend on the types
  - if `U` is identical, or a subset with only [marker traits] differing, the pointer will remain the same

    This cast has zero-overhead.

    ```
    a: ^dyn Foo & Bar & Mark0 & Mark1 = ...;
    
    // all of the following casts are allowed without changing the pointer
    _ = a as ^dyn Foo & Bar & Mark0 & Mark1;
    _ = a as ^dyn Foo & Bar & Mark1;
    _ = a as ^dyn Foo & Bar & Mark0;
    _ = a as ^dyn Foo & Bar;
    ```
  - if `U` is a subset with non-[marker traits] differing, the pointer to the data will be the same, but the metadata will be updated to contain the correct vtable for the new set of traits.
  
    > _Note_: This may incur some additional overhead to retreive or produce the new vtable.

    > _Note_: This is only allowed when compiler with the relavent type metadata

    ```
    a: ^dyn Foo & Bar = ...;
     
    // all of the following casts will incur a change in the metadata
    _ = a as ^dyn Foo;
    _ = a as ^dyn Bar;
    ```

  - if `U` is not a subset of the traits of `T`, the cast may only occur by using `as?` or `as!`,
    the pointer to the data will remain the same, but the metadata will be updated to contain the correct vtable for the new set of traits.

    This incurs additional overhead to ensure the cast is valid and to retrieve or create the new vtable.

    > _Note_: This is only allowed when compiler with the relavent type metadata

    ```
    a: ^dyn Foo & Bar = ...;
    
    // error: cannot cast to trait object type with different traits
    // _ = a as ^dyn Baz;
    
    // Instead, the following can be done
    _ = a as? ^dyn Baz;
    _ = a as! ^dyn Baz;
    ```
- `T` is a [trait object type], and `U` is a compatible `Sized` type, the cast may only occur by using `as?` or `as!`.
  The data pointer part of `^T` will be retained as the new pointer and all metadata will be discarded.

  This incurs additional overhead to ensure the cast is valid.

  > _Note_: This is only allowed when compiler with the relavent type metadata

  ```
  a: ^dyn Trait = ...;
  
  // error: cannot cast from an unsized pointer to a sized pointer
  // _ = a as ^Foo;
  
  // Instad, the following can be done
  _ = a as? ^Foo;
  _ = a as! ^Foo;
  ```
- `T` and `U` are slice types. the pointer to the data will remain the same, but the size will change depending on the sized of `T` and `U`
  - if `T` and `U` have the same size, the underlying metadata will remain unchanged.
  - if `T` and `U` differ in size, the size will be changed to the maximum size that will cause the resulting array to be in bounds of the underlying data.

  When using `as?` or `as!`, assuming a slice of `n` elements, the cast will fail if `sizeof(T) * n` is not an integer multiple of `sizeof(U)`.

> _Tooling_: Tools should inform the user that a cast between trait object types with different non-marker traits may incur additional overhead

> _Note_: To casts between 2 pointer with unsized types without the changes in the pointer happing as defined above, use the [transmute expression] to cast between these pointers.

### Reference to pointer semantics [↵](#built-in-casts-)

References can be directly cast to pointer.
This will cause any information about about the value before this cast to be lost.

The only way to reverse this cast, is to re-borrow the value stored in the pointer.

```
a_val := 42;

a: &i32 = &a_val;

// cast
_ = a as ^i32;
```

### Array and slice to pointer semantics [↵](#built-in-casts-)

Both pointers to arrays and slices can be directly cast to a pointer.
They are allowed to be casted to both single and multi-element pointers.

When casting from a pointer to a slice, the metadata associated with the slice will be lost.
Similarly, the compiler will lose all knowledge about the size of an array before casting it to a pointer.
It is therefore up to the user to ensure that the memory will not be accessed out of bounds.

When casting to a single-element pointer, only the first value of the array or slice will be available via this pointer.

Casting a sentinel terminated pointer is only allowed when casting from an sentinel terminated array or slice, i.e. `^[N:M]T` -> `[^:M]T` is allowed, but not `^[N]T` -> `[^:M]T`.

```
arr0_val := [ 1, 2, 3 ];
arr1_val: [3:0]i32 = [ 1, 2, 3 ];

arr0: ^[3]i32 = &raw arr0_val;
arr1: ^[3:0]i32 = &raw arr1_val
slice0: ^[]i32 = &[ 1, 2, 3 ];
slice1: ^[:0]i32 = &[ 1, 2, 3 ];

// casts
_ = arr0 as ^i32;
_ = arr0 as [^]i32;

_ = arr1 as ^i32;
_ = arr1 as [^]i32;
_ = arr1 as [^:0]i32;

_ = slice0 as ^i32;
_ = slice0 as [^]i32;

_ = slice1 as ^i32;
_ = slice1 as [^]i32;
_ = slice1 as [^:0]i32;


// error cannot cast for a non-sentinel terminated array to a sentinel terminated pointer
_ = arr0 as [^:3];

// error cannot cast for a non-sentinel terminated array to a sentinel terminated pointer
_ = slice0 as [^:3];
```

### Array to slice pointer semantics [↵](#built-in-casts-)

Casting from a pointer to an array to a pointer to a slice will, in addition to copying the data's address, also encode the size of the array within the slice pointer, as would be expected for any slice pointer.

This cast is functionally equivalent to `&arr[..] as ^[]T`.

This does cause some slight overhead to storing this size into the pointer to the array, which is mininal, as the compiler already know the size of the array at this point.

```
arr0_val := [ 1, 2, 3 ];
arr1_val: [3:0] = [ 1, 2, 3 ];

arr0: ^[3]i32 = &raw arr0_val;
arr1: ^[3:0]i32 = &raw arr1_val;

_ = arr0 as ^[]i32;

_ = arr1 as ^[]i32;
_ = arr1 as ^[:0]i32;
```

### Function item to raw function semantics [↵](#built-in-casts-)

References or pointers to function items can be directly cast to a reference of pointer to a raw function type, with the exception of casting from a pointer to a reference.

This cast has zero overhead, as the pointer will remain the same.

The function item and the raw trait function must be compatible with each other, meaning:
- non-`async` functions may only be cast to non-`async` function pointers
- `async` functions may be cast to both non-`async` and `sync` function pointers

> _Note_: In the future, cast from `async` to non-`async` might not be permitted, this will depend on how the exact internal representation of `async` functions will be decided

```
fn foo();

foo_ref := &foo;
foo_ptr := &raw foo;

// casts
_ = foo_ref as &fn();
_ = foo_ptr as ^fn();
```

### Function item to function trait semantics [↵](#built-in-casts-)

References or pointer to function items can be directly cast to a refernce or pointer to a trait object of a supported, with the exception of casting from a pointer to a reference.

```
fn foo();

foo_ref := &foo;
foo_ptr := &raw foo;

// cast
_ = foo_ref as &FnOnce();
_ = foo_ptr as ^FnOnce();

// error: cannot cast function item to an unsupported function trait
_ = foo_ptr as ^FnMut();
```

> _Todo_: Ensure function traits are correct


### Closure to raw function semantics [↵](#built-in-casts-)

Similary to function items, a refernce or pointer to closures can also be cast to a reference of pointer to a raw function type, with the exception of casting from a pointer to a reference.

This comes with the additional caveat that the closure is not allowed to capture any external variables.

```
closure := fn{ 1 + 1 };

closure_ref := &closure;
closure_ptr := &raw closure;

// cast
_ = closure_ref as &fn();
_ = closure_ptr as ^fn();
```

### Closure to function trait semantics [↵](#built-in-casts-)


References or pointer to closures can be directly cast to a refernce or pointer to a trait object of a supported, with the exception of casting from a pointer to a reference.

Unlike closure to raw function casts, this cast has no limitation to capturing any variables, as the closure will be refered to according to the used interface.

```
a := 1;

closure := fn{ a + 1 };

closure_ref := &closure;
closure_ptr := &raw closure;

// cast
_ = closure_ref as &FnOnce();
_ = closure_ptr as ^FnOnce();

// error: cannot cast closure to an unsupported function trait
_ = closure_ptr as ^FnMut();
```

> _Todo_: Ensure function traits are correct




[array to pointer cast]:             #array-and-slice-to-pointer-semantics-
[slice to pointer cast]:             #array-and-slice-to-pointer-semantics-
[array to slice pointer cast]:       #array-to-slice-pointer-semantics-
[enum cast]:                         #enum-cast-semantics-
[function to function trait cast]:   #function-item-to-function-trait-semantics-
[function to raw function cast]:     #function-item-to-raw-function-semantics-
[integer to character cast]:         #integer-to-character-semantics-
[numeric cast]:                      #numeric-cast-semantics-
[pointer to address cast]:           #pointer-to-address-semantics-
[pointer to pointer cast]:           #pointer-to-pointer-semantics-
[primitive to numeric cast]:         #primitive-to-numeric-semantics-
[reference to pointer cast]:         #reference-to-pointer-semantics-

[closure to function trait cast]:    #closure-to-function-trait-semantics-
[closure to raw function cast]:      #closure-to-raw-function-semantics-

[built-in casts]:                    #built-in-casts-


[transmute expression]:         ./transmute-expressions.md
[rounding mode]:                ../attributes/code-generation.md#rounding-
[function item]:                ../items/functions.md
[marker traits]:                ../items/traits.md#marker-traits- "Todo: Section does not exist yet"
[coercions]:                    ../type-system/type-coercions.md
[float]:                        ../type-system/types/builtin-types/floating-point-types.md
[integer]:                      ../type-system/types/builtin-types/integer-types.md
[boolean]:                      ../type-system/types/builtin-types/boolean-types.md
[character]:                    ../type-system/types/builtin-types/character-types.md
[enum]:                         ../type-system/types/composite-types/enum-types.md
[assigned discriminant values]: ../type-system/types/composite-types/enum-types.md#assigning-discriminant-values-
[explicit discriminant]:        ../type-system/types/composite-types/enum-types.md#discriminant-
[fieldless enums]:              ../type-system/types/composite-types/enum-types.md#fieldless-enums-
[flag enums]:                   ../type-system/types/composite-types/enum-types.md#flag-enums-
[unit variants]:                ../type-system/types/composite-types/enum-types.md#unit-variants-
[final slice field]:            ../type-system/types/composite-types/struct-types.md#final-slice-fields-
[raw function type]:            ../type-system/types/function-like-types/raw-function-types.md
[closure]:                      ../type-system/types/function-like-types/closure-types.md
[closures]:                     ../type-system/types/function-like-types/closure-types.md
[`volatile`]:                   ../type-system/types/pointer-like-types/pointer-types.md#volatile-pointers-
[`allowzero`]:                  ../type-system/types/pointer-like-types/pointer-types.md#allowzero-
[alignment]:                    ../type-system/types/pointer-like-types/pointer-types.md#alignment-
[trait object type]:            ../type-system/types/trait-types/trait-object-types.md
[trait object types]:           ../type-system/types/trait-types/trait-object-types.md

[address functions]:                 #pointer-to-address-semantics- "Todo: link to docs"
[`transmute`]:                       #type-cast-expressions "Todo: link to docs"