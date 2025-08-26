# Primitive types
```
<primitive-type> := <numeric-type>
                  | <signed-type>
                  | <floating-point-type>
                  | <boolean-type>
                  | <character-type>
```

A primitive type is a type that exists directly within the langauge and can be handled specially by the compiler.
These are commonly types that fit in machine register and have specialized instruction for these types.

Primitive types are susceptible to [illegal behavior](../../illegal-behavior.md)

> _Todo_: Support for different endianess in types

#### Unsigned types

```
<unsigned-type> := 'u8' | 'u16' | 'u32' | 'u64' | 'u128'
```

An unsigned type represents a natural number (including 0).

Unsigned numbers can generally represent a number between 0 and `(2^n)-1`

Below is a table of supported unsigned integer types:

Type   | Bit width | Min value | Max value 
-------|-----------|-----------|-----------------------------------------------------
`u8`   | 8-bit     | 0         | 255
`u16`  | 16-bit    | 0         | 65_535
`u32`  | 32-bit    | 0         | 4_294_967_295
`u64`  | 64-bit    | 0         | 18_446_744_073_709_511_615
`u128` | 128-bit   | 0         | 340_282_366_920_938_463_463_374_607_431_768_211_455

Both the size and alignment of the unsigned integers are defined by their bit-width.

All but `u128` are generally representable in a CPU register and have native instructions, if any type does not have native instructions, the program will fall back to 'emulating' these types.

In addition to the above types, there is also another unsigned type: `usize`.
`usize` is an unsigned type with the size of a machine-register.
Most commonly, this means that `usize` will be 32-bits on a 32-bit machine, and 64-bits on a 64-bit machine.

> _Note_: Minoa makes no guarantee on the size of `usize` and the programmer will therefore require explicit care when using this type.

> _Note_: A general rule is to prefer unsigned types whenever negative numbers aren't required. The programme does need to pay attention that the result of any intermetiate result cannot be a negative value when unsigned types are used.

#### Signed types

```
<signed-type> := 'i8' | 'i16' | 'i32' | 'i64' | 'i128'
```

An unsigned type represents a integral number.

Unsigned numbers can generally represent a number between `-2^(n-1)` and `(2^(n-1))-1`

Below is a table of supported unsigned integer types:

Type   | Bit width | Min value                                            | Max value 
-------|-----------|------------------------------------------------------|-----------------------------------------------------
`i8`   | 8-bit     | -128                                                 | 127
`i16`  | 16-bit    | -32_768                                              | 32_767
`i32`  | 32-bit    | -2_147_483_648                                       | 2_147_483_647
`i64`  | 64-bit    | -9_223_372_036_854_775_808                           | 9_223_372_036_854_775_807
`i128` | 128-bit   | -170_141_183_460_469_231_731_687_303_715_884_105_728 | 170_141_183_460_469_231_731_687_303_715_884_105_727

Both the size and alignment of the signed integers are defined by their bit-width.

All but `i128` are generally representable in a CPU register and have native instructions, if any type does not have native instructions, the program will fall back to 'emulating' these types.

In addition to the above types, there is also another signed type: `isize`.
`isize` is an unsigned type with the size of a machine-register.
Most commonly, this means that `usize` will be 32-bits on a 32-bit machine, and 64-bits on a 64-bit machine.

> _Note_: Minoa makes no guarantee on the size of `isize` and the programmer will therefore require explicit care when using this type.

#### Floating-point types

```
<floating-point-type> := 'f16' | 'f32' | 'f64' | 'f128'
```

A floating point type represent the same sized type as defined in the IEEE-754-2008 specification.

Below is a table of supported floating-point types:

Type   | Bit width | Mantissa bits      | Exponent bits | Min value  | Max value   | Smallest value | Significant decimal digits | Notes
-------|-----------|--------------------|---------------|------------|-------------|----------------|----------------------------|------
`f16`  | 16-bits   | 10 (11 implicit)   | 5             | 6.55e+5    | -6.55e+5    | 6.10e-5        | 3                          |
`f32`  | 32-bits   | 23 (24 implicit)   | 8             | 3.40e+38   | -3.40e+38   | 1.17e-38       | 6                          |
`f64`  | 64-bits   | 52 (53 implicit)   | 11            | 1.80e+308  | -1.80e+308  | 2.23e-308      | 15                         |
`f80`  | 80-bits   | 1 + 63             | 15            | 1.19e+4932 | -1.19e+4932 | 3.36e-4932     | 15                         | Does not have implicit bit, but explicit integer bit, i.e. `1 + ...`
`f128` | 128-bits  | 112 (113 implicit) | 15            | 1.19e+4932 | -1.19e+4932 | 3.36e-4932     | 34                         |

Both the size and alignment of the floating points are defined by their bit-width.

Most commonly, only `f32` and `f64` are implemented in hardware and have native instructions (like `f80` only being on x86), if any type does not have native instructions, the program will fall back to 'emulating' these types.

> _Note_: Subnormal literals, meaning numbers starting with `0x0.` and followed by any non-zero number in the mantissa and/or exponent, are currently not supported

> _Todo_: could include other floating-point types if wanted

#### Boolean types

```
<boolean-type> := 'bool' | 'b8' | 'b16' | `b32' | 'b64'
```

A boolean type is a primitive type that can be used to define 1 out of 2 possible states: `true` or `false`.
As a boolean only can represent these 2 values, there are also only 2 valid bit representations for a boolean.
These are `0x0` and `0x1`, meaning that the lower bit is set to the value, and all other bits are set to 0.
Any other bitpattern is undefined behavior.

Below is a table of supported boolean types:

Type   | Bit-width | Bit-width in bitfield
-------|-----------|----------------------
`bool` | 8-bits    | 1-bit
`b8`   | 8-bits    | 8-bits
`b16`  | 16-bits   | 16-bits
`b32`  | 32-bits   | 32-bits
`b64`  | 64-bits   | 64-bits

Both the size and alignment of the booleans are defined by their bit-width.
When used in a bitfield, specific bit-with mentioned above is used.

Most commonly, `bool` will be the most common version to use, with sized version mainly being used for 2 reasons:
- `b8` is useful to require booleans to keep a 1 byte width within a bitfield, as `bool` will automatically become a 1-bit value
- in usecase where a specific bit-width is required, e.g. `b32` as an equivalent to Window's `BOOL`

#### Character types

```
<character-type> := 'char' | 'char7' | 'char8' | 'char16' | 'char32'
```

A character type is primitive type that can represent unicode characters.

Below is a table of supported character types

Type     | Meaning           | Bit-width | Bit-width in bitfield | Valid range
---------|-------------------|-----------|-----------------------|------------------------------------------
`char`   | unicode codepoint | 32-bits   | 32-bits               | 0x000000 - 0x00D7FF & 0x00E00 - 0x10FFFF
`char7`  | 7-bit ANSI        | 8-bits    | 7-bit                 | 0x00     - 0x7F
`char8`  | 8-bit ANSI        | 8-bits    | 8-bits                | 0x00     - 0xFF
`char16` | utf-16 codepoint  | 16-bits   | 16-bits               | 0x0000   - 0xFFFF
`char32` | uft-32 codepoint  | 32-bits   | 32-bits               | 0x000000 - 0x10FFFF

Both the size and alignment of the characters are defined by their bit-width.
When used in a bitfield, specific bit-with mentioned above is used.

If a character has a value outside of its valid range, it is [illegal behavior](../../illegal-behavior.md)
