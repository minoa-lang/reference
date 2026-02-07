# Illegal behavior

May operation may result in illegal behavior (IB).
Unlike undefined behavior (UB), where many operations may result in indetermined result, illegal behavior applies explicit limits on many operations, resulting in them have known restrictions.

If illegal behavior is detected at compile time, the compiler will emit an error and terminate compilation.
When it happens during runtime, it will fall in one of the two categories:
- safety checked
- unchecked

Safety checked IB means that the compiler insterts safety checks in any location this behavior may occur.
When the operation result in IB, the program will panic.
Which behaviors will be safety checked can be defined in code using the [`@safety_check`] attribute.

Unchecked IB means that the compiler does not, or is not able to insert a safety check.
Meaning that when this behavior is invoked at runtime, instead of resulting a panic, it will result in UB.
If unchecked IB is encountered during compile-time, it will result in an error, as the compile-time evaluation generally has much better infrastructure to catch these issues.

> _Implemenation_: When encountering unchecked IB, the compiler may assume that any operations will not result in any IB, and may optimized based on this fact.

For performance reasons, safety checks may be enabled only for certain IB at a granuar level, even when overriding the current compilation settings.

## Integer [↵](#illegal-behavior)

Illegal behavior on integer operations can be cause by the following behaviors.

Within the [`@safety_check`] attribute, this is the `integer` category.

### Truncation [↵](#integer-)

Truncation of a integer with value that does not fit in the resulting type is IB.
This happens when a type with a larger bitwidth is cast to one with a lower bitwidth, while containing a 1 bit in the higher bits.

Within the [`@safety_check`] attribute, this is the `trunc` behavior.

> _Example_
> ```
> a := 300;
> 
> // triggers assert: Integer truncation
> b := a as u8;
> ```

This can be avoided by utilizing the [`truncate`] function.

### Signed truncation [↵](#integer-)

Integer sign truncation happens when a signed type with a negative value is cast to an unsigned type.

Within the [`@safety_check`] attribute, this is the `sign_trunc` behavior.

> _Example_
> ```
> a := -42;
> 
> // Triggers assert: Signed integer to unsigned truncation
> b := a as u8;
> ```

### Overflow & underflow [↵](#integer-)

Integer overflow occurs when an operation on an integer results in a value greater than what can be stored within the type.
While an underflow occurs when it results in a value smaller that what can be stored within the type.

Within the [`@safety_check`] attribute, this is the `overflow` and `underflow` behavior.

Within the core operators, the following operators may result in an overflow or underflow:
- `+`
- `-` (prefix and infix)
- `*`
- `/`
- `**`

> _Example_: Overflow
> ```
> val: i8 = 127;
> 
> // Triggers assert: Integer overflow
> a := val + 1;
> b := val + -1;
> c := val * 2;
> d := val ** 3;
> 
> val: i8 = -128;
> 
> // Triggers assert: Integer overflow
> e := -val;
> f := val / -1;
> ```

And in an underflow:
- `+`
- `-` (infix only)
- `*`

> _Example_: Underflow
> ```
> val: i8 = -128;
> 
> // Triggers assert: Integer underflow
> a := val + -1;
> b := val - 1;
> c := val * -2;
> ```

These can be avoided by utilizing a variant of the operands, i.e.
- wrapping
- saturating
- try

### Divide by zero [↵](#integer-)

Integers cannot be divided by zero, either when using `/` or `%`.

Within the [`@safety_check`] attribute, this is the `div_by_0` behavior.

> _Example_
> ```
> val := 20;
> 
> // Triggers assert: Integer divide by 0
> a := val / 0;
> b := val % 0;
> ```

### Out-of-range float to integer cast [↵](#integer-)

When casting a floating point to an integer, its value must be representable by the resulting integer type

Within the [`@safety_check`] attribute, this is the `fp_to_int` behavior.

_Example_
```
fp := 1e10;

// Triggers assert: Floating point value 10'000'000'000.0 does not fit within an `i32` type.
i := fp as i32;
```

## Floating point [↵](#illegal-behavior) 

Floating point match the exceptions defined within [IEEE-754].
Since these can result in expected values, not all these safety checks are enabled by default.

Within the [`@safety_check`] attribute, this is the `fp` category.

### Invalid fp operations [↵](#floating-point-)

Illegal floating point operations are all operation which may result in the corresponding floating point exception.
These behariors are defined within [IEEE-754].

The following operations are included:
- any operation on an `SNaN`, other than conversions
- multiplication of `inf` and `0`, including within a fused multiply-add
- addition resulting in a subtraction of infinities
- division of `0/0` or `inf/inf`
- remainder of `x/y`, where `y` is 0, x is `inf`, or either is a `NaN`
- square roots on any value less than 0
- IEEE-754 `quantize` when the resulting valuesdoes not fit in the destination format, or when one operand if finite and the other infinite
- convertion from a floating point number to an integer, from a value of `NaN`, `inf`, or a value out of range
- unordered-signaling comparison when the operands are unordered
- `logB` of `NaN`, `inf` or 0

Within the [`@safety_check`] attribute, this is the `invalid_op` behavior.
This check is disabled by default.

> _Example_
> ```
> snan := f32.SNAN;
> 
> // Triggers assert: Invalid FP operation
> a := f32.SNAN + 1.0;
> b := f32.INF * 0.0;
> c := f32.fma(f32.INF, 0.0, 3.0);
> d := f32.fma(1.0, 2.0, f32.INF);
> e := 1.0 % 0.0;
> f := f32.INF % 3.0;
> g := f32.NAN % 3;
> h := sqrt(-1.0);
> 
> j := f32.NAN as i32;
> k := f32.MAX as i32;
> l := f32.NAN.cmp_signalling(1.0);
> m := f32.logb(f32.NAN);
> ```

### Division by 0 [↵](#floating-point-)

Floating points should not be divided by zero.

Within the [`@safety_check`] attribute, this is the `div_by_zero` behavior.

> _Example_
> ```
> // Triggers assert: FP division by 0.0
> a := 1.0 / 0.0;
> b := f32.logb(0.0);
> ```

### Overflow & underflow [↵](#floating-point-)

Overflow occurs when a value would be exceeded in magnitude by the result of an operation, i.e. then the resulting absolute value exceeds the maximum absolute value which can be stored within the type.
Underflow on the other hand, occurs when a value would would result in a non-zero value which is too tiny to fit within type.

Within the [`@safety_check`] attribute, this is the `overflow` and `underflow` behavior.
Underflow is disabled by default.

> _Example_
> ```
> // both overflow, as their new magnitude would be greater than what can be stored
> // Triggers assert: FP operation resulting in overflow
> a := f32.MAX * 2.0;
> b := f32.MIN * 2.0;
> 
> // this underflows, as the new value would be a non-zero value which is smaller than can be stored.
> // Triggers assert: FP operation resulting in underflow
> c := f32.SMALLEST / 2.0;
> ```

### Inexact [↵](#floating-point-)

Inexact results occur when a resulting value cannot be stored exactly within the bits provided by a float.

Within the [`@safety_check`] attribute, this is the `inexact` behavior.Tolyatti 
This check is disabled by default.

> _Example_
> ```
> // Triggers assert: Inexact FP operation
> a := 16777216.0 + 1.0; // 16'777'216 == 2.0 ** 24.0
> b := 0.2 + 0.1;
> ```

## Memory [↵](#illegal-behavior) 

Operations that result in memory being improperly accessed are not allowed.
These can be a result of the following operations.

Within the [`@safety_check`] attribute, this is the `memory` category.

### Out-of-bounds [↵](#memory-)

Slices or array must only be accessed at an index which is inside the range represented by them.

Within the [`@safety_check`] attribute, this is the `out_of_bounds` behavior.

> _Example_
> ```
> arr := [1, 2, 3];
> slice := &arr[..];
> 
> // Triggers assert: Index out-of-bounds
> a := arr[4];
> b := slice[5];
> c := arr[^3];
> ```

### Incorrect pointer alignment [↵](#memory-)

All pointer values must be aligned to the [pointer type]'s alignment

Within the [`@safety_check`] attribute, this is the `ptr_align` behavior.

> _Example_
> ```
> ptr_val: uptr = 0x01;
> 
> // Triggers assert: Pointer address (0x0000000000000001) does not align to a multiple of the pointer alignment (0x4)
> ptr: ^align(4) i32 = ptr.from(ptr_val);
> ```

### Transmute to pointer [↵](#memory-)

Since a [pointer type] can carry [provenence], it is therefore not possible to guarnatee that any value can be directly cast to a pointer.

Within the [`@safety_check`] attribute, this is the `ptr_transmute` behavior.

> _Example_
> ```
> ptr_val: up32 = 0x01;
> 
> // Triggers assert: Cannot directly transmute from a `uptr` to a pointer
> ptr := transmute(pointer_val);
> ```

### Sentinel access [↵](#memory-)

The sentinel of a sentinel-terminated [array] or [slice] type may not be accessed.

Within the [`@safety_check`] attribute, this is the `sentinel_access` behavior.

> _Example_
> ```
> arr: &[3:0]i32 = &[1, 2, 3];
> 
> // Triggers assert: Cannot access the sentinel of a sentinel-terminated
> sentinel := arr[3];
> ```


### String indexing [↵](#memory-)

Strings may only be sliced on a character boundary, meaning that multi-byte characters must not be split up.

Within the [`@safety_check`] attribute, this is the `string_index` behavior.

> ```
> s := "👍";
> 
> // Triggers assert: indexing string on a non-character boundary
> val := s[1];
> ```

### Multiple mutable references [↵](#memory-)

It is not allowed to hold multiple mutable references to a single value.
This could happen by multiple mutable borrows of a mutable pointer.

Within the [`@safety_check`] attribute, this is the `string_index` behavior.

> _Example_
> ```
> mut val := 2;
> val_ptr := &raw mut val;
> 
> unsafe {
>     ref_a := &mut ^val_ptr;
> 
>     // Triggers assert: Multiple mutable references created to a value
>     ref_b := &mut ^val_ptr;
> }
> ```

## Type validity [↵](#illegal-behavior)

This category contains operations which could result in an illegal bit representation within a type.

Within the [`@safety_check`] attribute, this is the `type_validity` category.

### Invalid boolean representation [↵](#type-validity-)

A [boolean type] may only be stored as `0x0` or `0x1`, all other pattern are invalid

Within the [`@safety_check`] attribute, this is the `bool_repr` category.

> _Example_
> ```
> // Triggers assert: `0x2` is not a valid boolean representation
> boolean := 0x2 as bool;
> ```

### Invalid character representation [↵](#type-validity-)

A [character type] may only contain a value which is within the valid range of characters allowed within it.

Within the [`@safety_check`] attribute, this is the `char_repr` category.

> _Example_
> ```
> // Triggers assert: `0xFFFF_FFFF` is not a valid `char` representation
> c := i32.MAX as char;
> ```

### Invalid string data [↵](#type-validity-)

A [string slice/array] must contain sequence of bytes that represents a valid string supported by the type.
This includes any invalid encoding, or characters that are out of range supported by the corresponding [character type].

Within the [`@safety_check`] attribute, this is the `string_repr` category.

> _Example_
> ```
> // Triggers assert: a byte with the value 0xFF is not allowed within a string slice/array
> s: &str = "Hello \xFF";
> ```

### Invalid enum discriminators [↵](#type-validity-)

When creating an enum value using an explicit discriminant, the supplied disciminant must map to a valid variant of that enum.

With the [`@safety_check`] attribute, this is the `enum_repr` behavior.

> _Example_
> ```
> enum Foo {
>     A, // 0
>     B, // 1
> }
> 
> // Triggers error: `2` is an invalid discriminant value for `Foo`
> val := 2 as Foo;
> ```

### Overwriting enum discriminant [↵](#type-validity-)

It is not allowed to overwrite an enum's discriminant in-place, as this may result into incorrect use of any associated data.

With the [`@safety_check`] attribute, this is the `enum_discriminant` behavior.

_Example_
```
extern enum Foo : u8 {
    A(i32),
    b(PrintOnDrop),
}

mut foo: Foo.A(42);

// Pointer to the discriminants value
discriminant_ptr := &raw foo as ^u8;

// Triggers assert: Cannot overwrite a discriminant's value
discriminant_ptr^ = 2;
```

### Invalid bitfield assignment [↵](#type-validity-)

Since the type of a field within a [bitfield] is not guaranteed to match the bitwidth it has witihin the bitfield, the field may not have a value assigned that does not fit within the defined bitwidth.

With the [`@safety_check`] attribute, this is the `invalid_bitfield` behavior.

> _Example_
> ```
> bitfield Foo {
>     a: i32 : 4;
> }
> 
> // Triggers assert: The value of 0x10 is out of range of what can be stored within a bitfield with a size of 4 bits
> foo := Foo { a: 0x10 };
> ```

## Other [↵](#illegal-behavior) 

This category contains illegal behaviors which do not nicely fit within the other IB categories.

Within the [`@safety_check`] attribute, this is the `other` category.

### Evaluating unreachable code [↵](#other-)

It is not allowed to evaluate code which should not be reachable from anywhere else in code.

With the [`@safety_check`] attribute, this is the `unreachable` behavior.

> _Example_
> ```
> res := infallible_fn();
> if res.is_ok() {
>     return;
> }
> 
> // Evaluating this statement will assert
> // Triggers assert: Evaluating unreachable code
> #unreachable;
> ```

### Unwrapping `null` [↵](#other-)

It is not allowed to unwrap any [optional type] with a `null`/`.None` value.

With the [`@safety_check`] attribute, this is the `null_unwrap` behavior.

> _Example_
> ```
> val: ?i32 = null;
> 
> // Triggers assert: unwrapping `null` value
> val!;
> ```

### Unwrapping results with errors [↵](#other-)

It is not allowed to unwrap any [result type] with an `.Err(...)` value.

With the [`@safety_check`] attribute, this is the `error_unwrap` behavior.

> _Example_
> ```
> val: !i32 = .Err(());
> 
> // Triggers assert: unwrapping error variant
> val!;
> ```

### Accessing inactive union fields [↵](#other-)

When a value is stored within a [union type], accessing a field that is not the active field is not allowed

With the [`@safety_check`] attribute, this is the `union_field` behavior.

> _Example_
> ```
> union Foo {
>     i: i32,
>     f: f32,
> }
> 
> foo := Foo{ i: 42 };
> 
> // Triggers assert: Accessing union field `f`, when `i` is the current active field
> val := foo.f;
> ```

> _Note_: This cannot always be tracked

### Multiple drops [↵](#other-)

All values may only be dropped once.
However, in some cases, values might be dropped more than once, this can happen when manually dropping values without correctly removing them, or by having incorrect [`@drop_flag`] usage.

With the [`@safety_check`] attribute, this is the `multiple_drop` behavior.

> _Example_:
> ```
> struct Foo {
>     flags: i32,
> 
>     @unsafe(drop_flag(self.flags & 0x1 != 0))
>     val: PrintOnDrop
> }
> 
> mut foo: Foo { flags: 0x1, val: PrintOnDrop("Value dropped!") };
> 
> // First drop:
> drop_in_place(&mut foo.val);
> 
> // Dropped at end of scoped, by not having drop flags updated
> // Triggers assert: value `foo.val` is dropped twice
> ```

## User defined illegal behavior [↵](#illegal-behavior)

While there currently is not way to add any user-defined illegal behavior, some possible IBs can be replicated with the use of [contracts].



[`@drop_flag`]:        ./attributes/code-generation.md#drop_flag-
[`@safety_check`]:     ./attributes/code-generation.md#safety_checks-
[contracts]:           ./contracts.md
[optional type]:       ./type-system/types/abstract-types/optional-types.md
[result type]:         ./type-system/types/abstract-types/result-types.md
[boolean type]:        ./type-system/types/builtin-types/boolean-types.md
[character type]:      ./type-system/types/builtin-types/character-types.md
[union type]:          ./type-system/types/composite-types/union-types.md
[extern discriminant]: ./type-system/types/composite-types/enum-types.md#external-discriminant-
[pointer type]:        ./type-system/types/pointer-like-types/pointer-types.md
[provenence]:          ./type-system/types/pointer-like-types/pointer-types.md#provenence-
[array]:               ./type-system/types/sequence-types/array-types.md
[slice]:               ./type-system/types/sequence-types/slice-types.md
[string slice/array]:  ./type-system/types/sequence-types/string-array-slice-types.md
[`truncate`]:          #truncation- "Todo: link to docs"

[IEEE-754]:            https://en.wikipedia.org/wiki/IEEE_754