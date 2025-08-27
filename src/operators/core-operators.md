# Core operators

Core operators have no special meaning, but are defined within the core library and have a use for builtin types

## Comparison operators [↵](#core-operators)

```
<comparison-op> := <eq-op> | <iden-op> | <ord-op> | <total-ord-op>
<eq-op>        := '==' | '!='
<iden-op>      := '===' | '!=='
<ord-op>       := '<' | '<=' | '>' | '>=' | '<=>?'
<total-ord-op> := '<=>'
```

Comparison operators are infix operators.
Parentheses are required to chain comparisons, e.g. `a == b == c` is invalid, but `(a == b) == c` is valid (when the type are compatible).

Unlike most infix operators, the traits to overload these operators are used more generally to indicate when a type may be compared and will likely be assumed to define actual comparisons by functions that use these are trait bounds.
Code can then use these assupmptions when using the operators.

Comparison operator are [`by_ref`]
Unlike most binary operators, these operators implicitly take a shared borrow of their operands, evaluating them as place expressions.

The following operators and their respective trait functions are

Operator | Meaning                          | Trait method
---------|----------------------------------|----------------------
`==`     | Equal                            | `Equality::eq`
`!=`†    | Not equal                        | `Equality::ne`
`===`    | Identical                        | `Identity::identical`
`!==`‡   | Not Identical                    | N/A
`<`*     | Less than                        | `Ord::lt`
`<=`*    | Less than or equal to            | `Ord::le`
`>`*     | Greater than                     | `Ord::gt`
`>=`*    | Greater than or equal to         | `Ord::ge`
`<?`*    | Partial less than                | `Ord::partial_lt`
`<=?`*   | Partial less than or equal to    | `Ord::partial_le`
`>?`*    | Partial greater than             | `Ord::partial_gt`
`>=?`*   | Partial greater than or equal to | `Ord::partial_ge`
`<=>?`   | Partial order comparison         | `Ord::partial_cmp`
`<`*     | Total less than                  | `TotalOrd::lt`
`<=`*    | Total less than or equal to      | `TotalOrd::le`
`>`*     | Total greater than               | `TotalOrd::gt`
`>=`*    | Total greater than or equal to   | `TotalOrd::ge`
`<=>`    | Total order comparison           | `TotalOrd::cmp` 

† By default implemented in terms of `!(a == b)`

‡ Always implemented as `!(a === b)`

\* By default implemented based on `partial_cmp`

\** By default implemented based on `cmp`

The operators have the `Compare` precedence.

Below is an explenation of these traits:

> _Note_: This might be replaces with a simpler explanation, and have its definition moved into the `core` and `std` library

#### `Equality`

`Equality` is a trait that defines what types can be compared to each other, i.e. which are equal to each other.

This trait defines the `==` and `!=` operators.

Values implementing this can be compared to each other.

Equality does not neccesarily imply that a value is equal to itself, for example `f32::NAN != f32::NAN`

Equality has the following properties:
- reflexitivy, i.e. `x == x` is always true
- symmetry or communitativity, i.e. `a == b` implies `b == a`
- transitive, i.e. `a == b` and `b == c` implies `a == c`

There is no guarantee that a value is equal to itself.

When 2 values are compared, they might still have distinct bitpattern underlying the 

#### `Identity`

`Identity` is a trait is similar to `Equality`, but with some additional guarantees:

This trait defines the `===` and `!==` operators.

values implementing this can be used to check if 2 values are identical.

While identity technically has the same properties to equality, it has 1 major distinction that makes this point a bit moot.
Since identity is used to check if 2 values are exactly equal, there can only 1 value that is equal to a value, i.e. the value itself.

> _Note_: The identity operator does **not** imply that 2 values have the same memory location

> _Note_: While most implementation are a memcmp, some types, such as smart pointers, might change this definition to "pointing to the same address"

> _Note_: If `Identity` is implemented and `Equality` is not implemented (either derived or explicitly implemented), an implementation based on `Identity` will be added

#### `Ord`

`Ord` is a trait that defines what types can be ordered relative to each other, to be specific, a partial order.

This trait defines the following operators, which are split up in 3 categories:
- `<`, `<=`, `>` `>=`: comparison operators
- `<?`, `<=?`, `>?` `>=?`: partial-comparisom operator, similar to comparison operators, but may also indicate an 'undefined' comparison
- `<=>?`: Partial ordering, the main operator, of which the other operators, by default, derive their implementation from

`Ord` has  `Equality` as a super-trait.

Values implementing this be compared to each other and have an order between them.

Ordering has the following properties:
- communitativity, i.e. `a < b` implies `a > b`
- transitive, i.e. `a < b` and `b < c` implies `a < c`

#### `TotalOrd`

`TotalOrd` is a trait that defines what types can be ordered relative to each other using total ordering.

`TotalOrd` has `Equality` as a super-trait.

Total ordering defined the following operators:
- `<!`, `<=!`, `>!`, `>=!`: total-comparison operator
- `<=>`: total-ordering operator, the main operator, of which the other operators, by default, derive their implementation from

An example of how total ordering and partial ordering differ, `-0.0 == 0.0` in partial ordering, but `-0.0 < 0.0` in total ordering.

> _Todo_: Add 'identical' operators, i.e. === and !==, useful for thing like references without implicitly dereferning the parameter before hand

> _Note_: If `TotalOrd` is implemented and `Ord` is not implemented (either derived or explicitly implemented), an implementation based on `TotalOrd` will be added

## Range operators [↵](#core-operators)

```
<range-op> := '..' | '..='
```

Range operators can be prefix, infix, or postfix.

The range operators, like their name implies are used to generate a range between 2 values.

The following operators, their respecitive trait methods, resultant types, and ranges are

Operator     | Syntax        | Trait Method                     | Type               | Range
-------------|---------------|----------------------------------|--------------------|--------------------
Infix `..`   | `start..end`  | `Range::range`                   | `Range`            | start <= x < end
Postfix `..` | `start..`     | `RangeFrom::range_from`          | `RangeFrom`        | start <= x
Prefix `..`  | `..end`       | `RangeTo::range_to`              | `RangeTo`          | x <= end
Infix `..=`  | `start..=end` | `RangeInclusive::range_inc`      | `RangeInclusive`   | start <= x <= end
Prefix `..=` | `..=end`      | `RangeToInclusive::range_to_inc` | `RangeToInclusive` | x <= end

## Contains operator [↵](#core-operators)

```
<contains-op> := 'in' | '!in'
```

Contains operators are infix operators.p
A contains operator can be used to check if a value is contained within another value, e.g. if a value is contained by a range or collection.
There is both a positive and negated version.

This operator differs from other operators, by the fact that it can be a combination of a non-alphanumeric and alphanumeric characters.

The following operators and their respective trait functons are:

Operator | Meaning          | Trait method
---------|------------------|--------------------------
`in`     | Contains         | `Contains::contains`
`!in`†   | Does not contain | `Contains::not_contains`

† By default implemented in terms of `!(a in b)`

The operator has the `Contains` precedence.

## Or-else operator [↵](#core-operators)

```
<or-else-op> := '?:'
```

Or-else operators an infix operators.

The or-else works based on the value of the left-hand operand.
If the left-hand operand evaluates to a 'thruthy' value, the left hand operand is returned.
Otherwise if it evaluates to a non-'truthy' value, the right operand is evaluated.

'Truthy' can imply more than explicitly `false` or 'none' operations, i.e. `0` is not a 'thruthy' value. 

The associated trait is `OrElse`

The operator has the `Select` precedence.

## Pipe operators [↵](#core-operators)

```
<pipe-op> := '|>' | '<|'
```

Pipe operators are infix operators.

Pipe operators are used to pipe a value into another expresion, this can be done in 2 ways:
- chaining: where the left-hand operator is moved int the right-hand operand
- comsuming: the results of the right-hand operand is moved into the left-hand operand

When it comes to the operands of the chaining an consuming operator, they follow their respective `chain` and `consume` operator modifier's behaviors.

The associated traits for this operators are:
- `|>`: `PipeChain`
- `<|`: `PipeConsume`

The operator has the `Pipe` precedence.

## Other operators [↵](#core-operators)

The following section contains a list of other prefix, postfix and infix operators that weren't mentioned in their own individual sections

Prefix operators:

Operator | type                  | Trait | precedence | meaning                                        | Example
---------|-----------------------|-------|------------|------------------------------------------------|----------------------------------------
`+`      | numeric               | `Pos` | `Unary`    | unit operators, return the same value as given | `+a == a`
`-`      | signed/floating point | `Neg` | `Unary`    | negation                                       | `-a != -1 if a == 1` and `-(-a) == a`
`-%`     | signed/floating point | `Neg` | `Unary`    | wrapping negation                              | `-%128 == -128`
`!`      | bool                  | `Not` | `Unary`    | Logical not                                    | `!false == true`
`!`      | integer               | `Not` | `Unary`    | Bitwise not                                    | `!0 == usize::MAX` 


Infix/binary operators:

Operator | type                  | Trait          | precedence  | meaning                                                         | Example
---------|-----------------------|----------------|-------------|-----------------------------------------------------------------|----------------------------------------
`+`      | numeric               | `Add`          | `AddSub`    | Addition, panics on overflow (in debug)                         | `1 + 2 == 3`
`+%`     | integer               | `WrappedAdd`   | `AddSub`    | Addition, wraps on overflow                                     | `u32.MAX +% 1 == 0`
`+\|`    | integer               | `SaturateAdd`  | `AddSub`    | Addition, saturates on overflow                                 | `u32.MAX +\| 1 == u8.MAX`
`+?`     | integer               | `TryAdd`       | `AddSub`    | Addition, returns Some, or None on overflow                     | `1 +? 2 == Some(3)` or `u32.MAX +? 1 == None`
`-`      | numeric               | `Sub`          | `AddSub`    | Subtraction, panics on underflow (in debug)                     | `3 - 2 == 1`
`-%`     | integer               | `WrappedSub`   | `AddSub`    | Subtraction, wraps on underflow                                 | `0 -% 1 == u32.MAX`
`-\|`    | integer               | `SaturateSub`  | `AddSub`    | Subtraction, saturates on underflow                             | `0 -\| 1 == 0`
`-?`     | integer               | `TrySub`       | `AddSub`    | Subtraction, returns Some, or None on overflow                  | `1 -? 2 == Some(3)` or `0:u32 -? 1 == None`
`*`      | integer               | `Mul`          | `MulDivRem` | Multiplication, panics on overflow (in debug)                   | `2 * 3 == 6`
`*`      | floating point        | `Mul`          | `MulDivRem` | Multiplication, according IEEE-754-2008                         | `1.5 * 2.0 == 3.0`
`*%`     | integer               | `WrappedMul`   | `MulDivRem` | Multiplication, wraps on overflow                               | `128'u8 *% 3 == 128'u8`
`*\|`    | integer               | `SaturateMul`  | `MulDivRem` | Multiplication, saturates on overflow                           | `128'u8 *\| 3 == 255'u8`
`*?`     | integer               | `TryMul`       | `MulDivRem `| Multiplication, returns Some, or None on overflow               | `64'u8 *? 2 == Some(128)` or `128'u8 *? 2 == None`
`/`      | integer               | `Div`          | `MulDivRem` | Division, panics on divide by 0 (traps in non-debug)            | `6 / 2 == 3`
`/`      | floating point        | `Div`          | `MulDivRem` | Division, according IEEE-754-2008                               | `3.0 / 1.5 == 2.0`
`/?`     | integer               | `TryDiv`       | `MulDivRem `| Multiplication, returns Some, or None on divide by 0            | `128'u8 /? 2 == Some(2)` or `128'u8 /? 0 == None`
`%`      | numeric               | `Rem`          | `MulDivRem` | Remainder*, panics on divide by 0 (traps in non-debug)          | `5 % 2 == 2` or `7.0 % 1.5 == 1.0`
`%%`     | numeric               | `RemFloored`   | `MulDivRem` | Floored remainder**, panics on divide by 0 (traps in non-debug) | `5 % 2 == 2` or `7.0 % 1.5 == 1.0`
`\|`     | integer               | `Or`           | `BitOr`     | Bitwise or                                                      | `0x1010  \| 0x1100 == 0x1110`
`!\|`    | integer               | `Nor`          | `BitOr`     | Bitwise not-or                                                  | `0x1010 !\| 0x1100 == 0x0001`
`&`      | integer               | `And`          | `BitAnd`    | Bitwise and                                                     | `0x1010  & 0x1100 == 0x1000`
`!&`     | integer               | `Nand`         | `BitAnd`    | Bitwise not-and                                                 | `0x1010 !& 0x1100 == 0x0111`
`&!`     | integer               | `Mask`         | `BitAnd`    | Bitwise masking (and if inverse of `b`)                         | `0x1010 &! 0x1100 == 0x0010`
`~`      | integer               | `Xor`          | `BitXor`    | Bitwise xor                                                     | `0x1010  ~ 0x1100 == 0x0110`
`!~`     | integer               | `Xnor`         | `BitXor`    | Bitwise not-xor                                                 | `0x1010 !~ 0x1100 == 0x1001`
`<<`     | integer               | `Shl`          | `ShiftRot`  | Bit-shift left                                                  | `0x101 << 3 == 0x101000`
`<<\|`   | integer               | `SaturateShl`  | `ShiftRot`  | Bit-shift left, saturates if 1 bit is shifted out               | `0x1'u8 <<\| 8 == 0xFF`
`>>`     | signed                | `Shr`          | `ShiftRot`  | Bit-shift right (implicitly arithmetic shift)                   | `0x10..01  >> 3 == 0x11110..00`
`>>`     | unsigned              | `Shr`          | `ShiftRot`  | Bit-shift right (implicitly logical shift)                      | `0x10..01  >> 3 == 0x00010..00`
`>>-`    | integer               | `Shra`         | `ShiftRot`  | Explicit arithmetic bit-shift right                             | `0x10..01 >>- 3 == 0x11110..00`
`>>+`    | integer               | `Shrl`         | `ShiftRot`  | Explicit logical bit-shift right                                | `0x10..01 >>+ 3 == 0x00010..00`
`*<<`    | integer               | `Rotl`         | `ShiftRot`  | Bitwise rotate left                                             | `0x1010..1010 *<< 3 == 0x0..1010101`
`>>*`    | integer               | `Rotr`         | `ShiftRot`  | Bitwise rotate right                                            | `0x1010..1010 >>* 3 == 0x0101010..1`
`++`     | str/array             | `Concat`       | `AddSub`    | Concatinate 2 arrays (sizes must be known at compile time)      | `[1, 2, 3, 4] ++ [5, 6, 7, 8] == [1, 2, 3, 4, 5, 6, 7, 8]` or `"hello" ++ " " ++ "world" == "hello world"`
`**`     | numeric               | `Repetition`   | `MulDivRem` | Raise number to the power of `n`                                | `3 ** 4 = 81`
`**`     | str/array <-> integer | `Repetition`   | `MulDivRem` | Repeat an array N times (size must be known at compile time)    | `[1, 2] ** 3 == [1, 2, 1, 2, 1, 2]` or `"ab" ** 3 == "ababab"`

\* Uses truncating division, meaning `remainder = dividend % divisor` will return a value with the same sign as the dividend.

** Uses floored division, maning `remainder = dividend % divisor` will return a value with the same sign as the divisor.

> _Note_ When reading the above table in plain text, any occurance of `\|` is actually just `|`, but requires the `\` as it is otherwise interpreted as a the next value in the table



[`by_ref`]: ../operators.md#by_ref-