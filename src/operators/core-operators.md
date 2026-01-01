# Core operators
```
<core-ops> := <arith-ops>
            | <arith-assign-ops>
            | <bitwise-ops>
            | <bitwise-assign-ops>
            | <concat-op>
            | <repetition-op>
            | <range-ops>
            | <or-else-op>
            | <pipe-ops>
```

Core operators consist of all operators which are provided by the `core` library, but are not [special operators].
Meaning that core operators act like any operator which can be defined by a developers, except that they are always available and don't need to be explicitly imported, as they are part of the `core` prelude.

## Arithmetic [↵](#core-operators)
```
<arith-ops>        := '+' | '+%' | '+|' | '+?'
                    | '-' | '-%' | '-|' | '-?'
                    | '*' | '*%' | '*|' | '*?'
                    | '/' | '/?'
                    | '%' | '%%'
<arith-assign-ops> := '+=' | '+%=' | '+|=' | '+?='
                    | '-=' | '-%=' | '-|=' | '-?='
                    | '*=' | '*%=' | '*|=' | '*?='
                    | '/=' | '/?='
                    | '%=' | '%%='
```

Arithmetic operators are prefix and infix operators, which can apply an arithmetic operation on numeric values.

Below is a table of prefix arithmetic operators

operator | trait         | meaning                                             | example
---------|---------------|-----------------------------------------------------|-------------------------------
`+`      | `Pos`         | passed the input value back to the output           | `+1 == 1`
`-`      | `Neg`         | produces the negative version of a given value      | `-1 + 1 == 0` or `-(-1) == 1`

The `-` operator will panic if an under/overflow were to occur, for this reason, the `-%` provides a wrapping version, which will wrap the value if this were to happen.
This operator has the associated trait `WrappedNeg`.

> _Example_
> ```
> a: u8 = -128;
> 
> // will panic
> b := -a;
> 
> // will wrap, returning a value of 1
> b := -%a;
> ```


And below is table of infix arithmetic operators:

operator | trait        | precedence  | meaning                                                                                                            | example
---------|--------------|-------------|--------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------
`+`      | [`Add`]      | `AddSub`    | Adds the left- and right-hand operands                                                                             | `1 + 2 == 3`
`-`      | [`Sub`]      | `AddSub`    | Subtracts the right-hand operand from the left-hand operand                                                        | `3 - 2 == 1`
`*`      | [`Mul`]      | `MulDivRem` | Multiplies the left- and right-hand operand                                                                        | `2 * 3 == 6`
`/`      | [`Div`]      | `MulDivRem` | Divides the left-hand operand by the right-hand operand                                                            | `6 / 2 == 3`
`%`      | [`Rem`]      | `MulDivRem` | Calculate the remainder of dividing the left-hand operand by the right-hand operand (same sign as dividend)        | `7 % 3 == 1`, `-7 % 3 == -1`, `7 % -3 == 1`, and `-7 % -3 == -1`
`%%`     | [`RemFloor`] | `MulDivRem` | Calculate the floored remainder of dividing the left-hand operand by the right-hand operand (same sign as divisor) | `7 %% 3 == 1`, `-7 %% 3 == 1`, `7 %% -3 == -1`, and `-7 %% -3 == -1`
`**`     | [`Repeat`]   | `MulDivRem` | Exponentiates the left-hand operand to the right hand operand (common operator with the [repetition] operator)     | `3 ** 4 == 81`

All operations can be applied to numeric types, but follow their own semantics:
- [integer] types follow their semantics, meaning that any operation may result in an under/overflow, in addition to:
  - division by, or taking the remainder with an divisor of `0` will result in a panic
- [floating point] types follow the rules defined by IEEE-754, meaning they may result in special values instead of panic, depending on the current [float settings] and [safety checks]

> _Note_: `uptr` and `iptr` do not support all these operators, see the section below for more info

Similarly to a `-` prefix expression, these expressions will panic if an under/overflow were to occur, for this reason, there are 3 possible variants an operator may have.
These are:
- `Wrapped`: will wrap the value if an under/overflow were to occur
- `Saturate`: Will saturate the value of its minimum or maximum value if an under/overflow were to occur, respectively
- `Try`: returns an [optional] value, which will be `null` if an under/overflow were to occur, or when another panic were to occur normally

These operators are limited to operations on integers, where under/overflow may occur and are not defined.

The below table provides the variants for each operator:

base op | wrapped op | wrapped example         | saturate op | saturate example           | try op | try example
--------|------------|-------------------------|-------------|----------------------------|--------|-------------------------
`+`     | `+%`       | `254u8 +% 7u8 == 5u8`   | `+\|`       | `254u8 +\| 7u8 == 255u8`   | `+?`   | `254u8 +? 7u8 == null`
`-`     | `-%`       | `-126i8 -% 7i8 == -5u8` | `-\|`       | `-126i8 -\| 7i8 == -128i8` | `-?`   | `-126i8 -? 7i8 == null`
`*`     | `*%`       | `64u8 *% 5u8 == 65u8`   | `*\|`       | `64u8 *\| 5u8 == 255u8`    | `*?`   | `64u8 * 4u8 == null`
`/`     | n/a        | n/a                     | n/a         | n/a                        | `/?`   | `42 /? 0 == null`


Additionally, a set of assign operators exists as a variant on the infix operators.
The differences between these are:
- operators are suffixed with a `=`, i.e. infix `+` becomes assign `+=`
- trait is suffixed with `Assign`, i.e. infix `Add` becomes assign `AddAssign`
- function is suffixed with `_assign`, i.e. infix `add` becomes assign `add_assign`

### Pointer arithmetic [↵](#arithmetic-)

Pointer arithmetic (including `uptr` and `iptr`) supports only a limited set of operators, although they use the same semantics as integer types

These are define in the following table:

operator | lhs type | rhs type                    | result type | meaning                       | example
---------|----------|-----------------------------|-------------|-------------------------------|-----------------------------------------------------------------
`+`      | `^T`     | `iptr`, `usize`, or `isize` | `^T`        | pointer offset                | `a_ptr + ab_dist == b_ptr`
`+?`     | `^T`     | `iptr`, `usize`, or `isize` | `^T`        | try pointer offset            | `a_ptr +? max_offset == null`
`+`      | `uptr`   | `iptr`                      | `uptr`      | `uptr` offset                 | `a_uptr + ab_dist == b_uptr`
`+?`     | `uptr`   | `iptr`                      | `uptr`      | try `uptr` offset             | `a_uptr +? max_offset == null`
`-`      | `^T`     | `iptr`                      | `^T`        | pointer offset                | `b_ptr - ab_dist == a_ptr`
`-`      | `uptr`   | `uptr`                      | `iptr`      | `uptr` difference             | `a_uptr - b_uptr = ab_dist`
`*`      | `iptr`   | `usize` or `isize`          | `iptr`      | offset multiplication         | `ab_dist * 3 == ac_dist`
`*?`     | `iptr`   | `usize` or `isize`          | `iptr`      | try offset multiplication     | `ab_dist * usize.MAX == null`
`/`      | `iptr`   | `usize` or `isize`          | `iptr`      | offset division               | `ac_dist / 3 == ab_dist`
`/?`     | `iptr`   | `usize` or `isize`          | `iptr`      | try offset division           | `ac_dist /? 5 == null`, where `ac_dist` is not a multiple of 5

where `^T` represents any pointer type

Offset division may only happen when the offset is a multiple of the divisor.

## Bitwise [↵](#core-operators)
```
<bitwise-op>        := '|' | '!|'
                     | '&' | '!&' | '&!'
                     | '~' | '!~'
                     | '<<' | '<<|'
                     | '>>' | '>>-' | '>>+'
                     | '*<<' | '>>*'
<bitwise-assign-op> := '|=' | '!|='
                     | '&=' | '!&=' | '&!='
                     | '~=' | '!~='
                     | '<<=' | '<<|='
                     | '>>=' | '>>-=' | '>>+='
                     | '*<<=' | '>>*='
```

Binary operators are prefix and infix operators, which can apply an bit-wise operation on [integer] and [boolean] values.

These operators include 1 prefix operator: the NOT (`!`) operator.
It work on both integer and boolean types, with a slight difference in behavior:
- boolean: it negates the boolean, meaning that only the lower bit is affected, all other bits stay the same
- integer: it negates all bits within the value

> _Example_
> ```
> // boolean
> assert(!false == true);
> assert(!true == false);
> 
> // integer
> assert(!0b01010101u8 == 0b10101010u8);
> ```

The associated trait for the NOT operator is [`Not`].


Below is a table of all infix operators provided, these work on integer values

operator | trait         | function       | precedence | meaning                                                          | default impl    | example
---------|---------------|----------------|------------|------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------
`\|`     | [`BinOr`]       | `bin_or`       | `BinOr`    | bitwise OR                                                       | n/a             | `0b0101 | 0b0011 == 0b0111`
`!\|`    | [`BinOr`]       | `bin_nor`      | `BinOr`    | bitwise NOR                                                      | `!(lhs \| rhs)` | `0b0101 !| 0b0011 == 0b1101`
`&`      | [`BinAnd`]      | `bin_and`      | `BinAnd`   | bitwise AND                                                      | n/a             | `0b0101 & 0b0011 == 0b0001`
`!&`     | [`BinAnd`]      | `bin_nand`     | `BinAnd`   | bitwise NAND                                                     | `!(lhs & rhs)`  | `0b0101 !& 0b0011 == 0b1110`
`&!`     | [`BinAnd`]      | `bin_mask_out` | `BinAnd`   | Bitwise masking out of provided bits                             | `lhs & !rhs`    | `0b0101 &! 0b0011 == 0b0100`
`~`      | [`BinXor`]      | `bin_xor`      | `BinXor`   | bitwise XOR                                                      | n/a             | `0b0101 ~ 0b0011 == 0b0110`
`!~`     | [`BinXor`]      | `bin_xnor`     | `BinXOr`   | bitwise XNOR                                                     | `!(lhs ~ rhs)`  | `0b0101 !~ 0b0011 == 0b1001`
`<<`     | [`Shl`]         | `shl`          | `ShiftRot` | bitwise shift left                                               | n/a             | `0b101 << 3 == 0b101000`
`<<|`    | [`SaturateShl`] | `saturate_shl` | `ShiftRot` | saturating shift left (max value if 1 is shifted past first bit) | n/a             | `0b101u8 <<| 5 == 0b11111111u8`
`>>`     | [`Shr`]         | `shr`          | `ShiftRot` | bitwise shift right (shra when signed, shrl when unsigned)       | n/a             | `0b10101010i8 >> 3 == 0b11110101i8` or `0b10101010u8 >> 3 == 0b00010101u8`
`>>-`    | [`Shra`]        | `shra`         | `ShiftRot` | bitwise arithmetic shift left (shifts in sign bit)               | n/a             | `0b10101010u8 >>- 3 == 0b11110101u8`
`>>+`    | [`Shrl`]        | `shrl`         | `ShiftRot` | bitwise logical shift left (shifts in 0)                         | n/a             | `0b10101010u8 >>+ 3 == 0b00010101u8`
`*<<`    | [`Rotl`]        | `rotl`         | `ShiftRot` | bitwise rotate left                                              | n/a             | `0b11001010u8 *<< 3 == 0b01010110u8`
`>>*`    | [`Rotr`]        | `rotr`         | `ShiftRot` | bitwise rotate right                                             | n/a             | `0b11001010u8 >>* 3 == 0b01011001u8`

Some special semantics for operators are:
- shift and rotate instructions will shift by only the lower bits of the provided value, i.e `0b01010101 << 12` is equivalent to `0b01010101 << 4`, as it will do `0b01010101 << (12 & 0x7)`

> _Todo_: Check whether semantics are correct

Additionally, a set of assign operators exists as a variant on the infix operators.
The differences between these are:
- operators are suffixed with a `=`, i.e. infix `+` becomes assign `+=`
- trait is suffixed with `Assign`, i.e. infix `Add` becomes assign `AddAssign`
- function is suffixed with `_assign`, i.e. infix `add` becomes assign `add_assign`


[`Not`]: #bitwise- "Todo: link to docs"


## Concatination [↵](#core-operators)
```
<concat-op> := '++' | `++=`
```

The concatination operator is an infix operator, which concatinates 2 values.

The core only has 2 compile-time implemenations of this, these are:
- concatinating 2 string slices/arrays
- concatinating 2 arrays

The associated trait is [`Concat`].

> _Example_
> ```
> assert("hello " ++ "world!" == "Hello world!");
> assert([1, 2, 3] ++ [4, 5, 6] == [1, 2, 3, 4, 5, 6]);
> ```

Additionally, an assign operator exists as a variant of the infix operator.
This uses the [`ConcatAssign`] trait.

This is not supported by the provided implementation listed above, as it may produce a different type.

## Repetition [↵](#core-operators)
```
<repetition-op> := '**' | `**=`
```

The repetition operator is an infix operator, which allows for the repetition of a value.
Additionally, this is also used to do [exponentiation] of numeric values.

The core only has 2 compile-time implemenations of this, these are:
- repeat a string slice/array `N` times
- repeat an array `N` times

The associated trait is [`Repeat`].

_Example_
```
assert("hi! " ** 3 == "hi! hi! hi! ");
assert([1, 2] ** 3 == [1, 2, 1, 2, 1, 2]);
```

Additionally, an assign operator exists as a variant of the infix operator.
This uses the [`RepeatAssign`] trait.

This is not supported by the provided implementation listed above, as it may produce a different type.

## Range [↵](#core-operators)
```
<range-ops> := '..' | '..='
```

Range operators are a set of pre-, post- and infix operators, which are used to create a range from a lower and/or upper bound.

operator     | syntax        | Trait                | type               | range (all values of x for)
-------------|---------------|----------------------|--------------------|-----------------------------
prefix `..`  | `..end`       | `OpRangeTo`          | `RangeTo`          | `x < end`
infix `..`   | `start..end`  | `OpRange`            | `Range`            | `start <= x < end`
postfix `..` | `start..`     | `OpRangeFrom`        | `RangeFrom`        | `start <= x`
prefix `..=` | `..=end`      | `OpRangeInclusive`   | `RangeInclusive`   | `start <= x <= end`
infix `..=`  | `start..=end` | `OpRangeToInclusive` | `RangeToInclusive` | `x <= end`

> _Note_: The operator traits are prefixed with `Op`, to ensure distinct names between them and the corresponding range types when imported within the same scope.

These infix operators have a precedence of `Range`.

## Or-else [↵](#core-operators)
```
<or-else-op> := '?:' | '?='
```

The or-else operator, also known as the elvis operator, is an infix operator, which can select a value based on wether the left-hand operand has a 'thruthy' value.

Unlike the [catch operator], this operator does not work on erroneous types, but on any type which may indicate a 'thruthy' value.

A 'truthy' value is a type, which may represent a `true` or `false` value, e.g. `0` is a 'truthy' value for numeric types.
Which values represent a `true` of `false` value is dependent on its implementation.

> _Example_
> ```
> a := 0;
> 
> assert(a ?: 1 == 1);
> ```

The operator is a `lazy` operator.

The associated trait is [`OrElse`].

The operator has a precedence of `Select`.

Additionally, an assign operator exists as a variant of the infix operator
This will assign a value to the assignee, only if it does not have a 'truthy' value.

This uses the [`OrElseAssign`] trait.

## Pipe [↵](#core-operators)
```
<pipe-ops> := '|>' | '<|'
```

The pipe opeartors are infix operators, which allow values to be piped from one operation into another.

The pipe operator consist of 2 slightly different operators, as defined in the table below.

operator | trait           | op kind
---------|-----------------|-----------
`\|>`    | [`PipeChain`]   | `chain`
`<\|`    | [`PipeConsume`] | `consume`

Both pipe operators are the pipe equivalent version of their operator kind.

These operators have a precedence of `Pipe`.


[exponentiation]:    #arithmetic-
[repetition]:        #repetition-
[float settings]:    ../attributes.md "Todo: fix up link"
[safety checks]:     ../attributes.md "Todo: fix up link"
[special operators]: ./special-operators.md
[catch operator]:    ./special-operators.md#catch-
[optional]:          ../type-system/types/abstract-types/optional-types.md
[boolean]:           ../type-system/types/builtin-types/boolean-types.md
[integer]:           ../type-system/types/builtin-types/integer-types.md
[floating point]:    ../type-system/types/builtin-types/floating-point-types.md

[`Add`]:             #arithmetic- "Todo: link to docs"
[`Div`]:             #arithmetic- "Todo: link to docs"
[`Mul`]:             #arithmetic- "Todo: link to docs"
[`Rem`]:             #arithmetic- "Todo: link to docs"
[`RemFloor`]:        #arithmetic- "Todo: link to docs"
[`Repeat`]:          #arithmetic- "Todo: link to docs"
[`Sub`]:             #arithmetic- "Todo: link to docs"
[`BinAnd`]:          #bitwise- "Todo: link to docs"
[`BinOr`]:           #bitwise- "Todo: link to docs"
[`BinXor`]:          #bitwise- "Todo: link to docs"
[`SaturateShl`]:     #bitwise- "Todo: link to docs"
[`Shl`]:             #bitwise- "Todo: link to docs"
[`Shr`]:             #bitwise- "Todo: link to docs"
[`Shra`]:            #bitwise- "Todo: link to docs"
[`Shrl`]:            #bitwise- "Todo: link to docs"
[`Rotl`]:            #bitwise- "Todo: link to docs"
[`Rotr`]:            #bitwise- "Todo: link to docs"
[`Concat`]:          #concatination- "Todo: link to docs"
[`ConcatAssign`]:    #concatination- "Todo: link to docs"
[`PipeChain`]:       #pipe- "Todo: link to docs"
[`PipeConsume`]:     #pipe- "Todo: link to docs"
[`Repeat`]:          #repetition- "Todo: link to docs"
[`RepeatAssign`]:    #repetition- "Todo: link to docs"
[`OrElse`]:          #or-else- "Todo: link to docs"
[`OrElseAssign`]:    #or-else- "Todo: link to docs"