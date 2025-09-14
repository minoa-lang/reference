# Integer types

An integer type represents any natural or integral number (both including 0), depending on whether it is _signed_ or _unsigned_

Both the size and alignment of the integers are defined by their bit-width.
Outside of a bitfield, it will take up the minimum amount of bytes required to store the number.
More information can be found in the corresponding [type layout] section.

Integers types may have an arbitrary bitwidth that is <= 65536.
Any bit that is set outside of the range of an integer type will result in [illegal behavior].

Additonally, 2 kind of system dependent integers exists, these are either
- `usize` or `isize`: integer types with the same size as a machine register, e.g. 32-bits on a 32-bit machine, and 64-bits on a 64-bit machine.
- `uptr` or `iptr`: integer types with the same size as an address, these are often the same size as `usize` or `isize`

This split mainly exists to allow for systems where the memory pointer and the register size differ.

> _Reasoning_: Although it is unlikely for a modern system to have a different pointer and register size, in the future, there could be a backend implementation to support a system that does, 
>              even if this would not be done via the official compiler, but as a third party compiler or compiler extension.
>
>  Spliting this up into a specific `..size` and `..ptr` type, instead of a general `..size` also allows for additional info about the use of the value to be included within the type system.
>  For example, when taking a difference between 2 pointers, a `iptr` is return, indicating that the value is associated with a pointer type.

### Unsigned integer types [↵](#integer-types)

```
<unsigned-type> := ? \u{N}[le|be]\ where N is any integer <= 65536 ?
```

An unsigned type represents a natural number (including 0).

Unsigned types can generally represent a number between 0 and `(2^n)-1`

Below is a table of common supported unsigned integer types:

Type   | Bit width | Min value | Max value 
-------|-----------|-----------|-----------------------------------------------------
`u8`   | 8-bit     | 0         | 255
`u16`  | 16-bit    | 0         | 65_535
`u32`  | 32-bit    | 0         | 4_294_967_295
`u64`  | 64-bit    | 0         | 18_446_744_073_709_511_615
`u128` | 128-bit   | 0         | 340_282_366_920_938_463_463_374_607_431_768_211_455

All of the above mentined integers, with the exception of `u128` are generally representable in a CPU register and have native instructions, if any type does not have native instructions, the program will fall back to 'emulating' these types.

Although no guarantee can be make that `usize` and `uptr` are the same size, the langauge guarantees that:
- a `uptr` is able to represents any memory adress, and can be converted to from a [pointer type], this is not neccesarily a reversable operation.
This is because the cast can strip [provenence] information that is needed by the pointer.
- a `usize` is able to represent any index within a sequence type

> _Warning_: Minoa makes no guarantee on the size of `usize` and the programmer will therefore require explicit care when using this type.

> _Note_: A general rule is to prefer unsigned types whenever negative numbers aren't required. The programmer does need to pay attention that the result of any intermetiate result cannot be a negative value when unsigned types are used.

### Signed types [↵](#integer-types)

```
<signed-type> := ? \i{N}[le|be]\ where N is any integer <= 65536 ?
```

An signed type represents a integral number.

Signed numbers can generally represent a number between `-2^(n-1)` and `(2^(n-1))-1`

Below is a table of common supported signed integer types:

Type   | Bit width | Min value                                            | Max value 
-------|-----------|------------------------------------------------------|-----------------------------------------------------
`i8`   | 8-bit     | -128                                                 | 127
`i16`  | 16-bit    | -32_768                                              | 32_767
`i32`  | 32-bit    | -2_147_483_648                                       | 2_147_483_647
`i64`  | 64-bit    | -9_223_372_036_854_775_808                           | 9_223_372_036_854_775_807
`i128` | 128-bit   | -170_141_183_460_469_231_731_687_303_715_884_105_728 | 170_141_183_460_469_231_731_687_303_715_884_105_727

All of the above mentined integers, with the exception of `i128` are generally representable in a CPU register and have native instructions, if any type does not have native instructions, the program will fall back to 'emulating' these types.

Although no guarantee can be make that `isize` and `iptr` are the same size, the langauge guarantees that:
- an `isize` will always be able to store the maximum difference between 2 indexes into [sequence types], and
- `iptr` is always guaranteed to be able to store the maximum difference between 2 pointers to elements in the array

> _Warning_: Minoa makes no guarantee on the size of `isize` and the programmer will therefore require explicit care when using this type.

> _Note_: Minoa represents signed integer types using two's complement

### Endianness [↵](#integer-types)

Both signed and unsigned type additionally support a specific endianess to be provided.
This can be done by appending either `le` or `be` to the end of the type, specifying little- and big-endian respectively.
None of the machine dependent integers (i.e. `uptr`, `iptr`, `usize`, or `isize`) support a specific endianness.

These types have similar properties to their versions without an explicit endianess in terms of size and aligment, but have bytes that are laid out in the specific endianness.
In addition, these types don't allow any operator to be called on them and must first be converted to their native/machine-endian versions.

> _Note_: Trait implementations may still be provided for these types, but none of the core operators are implemented, as this might require intermediate conversions to native/machine-endianness, which might be unclear to the user, as they could expect all integer types to behave the same in compiled code.

### Integer langauge items [↵](#integer-types)

Both unsigned and signed integers are represented by the language items
```
@builtin(uint)
struct Uint[N: usize, E: Endianness] where N <= 65536;
```
and
```
@builtin(int)
struct Int[N: usize, E: Endianness] where N <= 65536;
```
respectively.

These types can be used to implement an associated item across multiple differently-sized integer types.

> _Note_: `usize` and `isize` are distinct types and can therefore not be represented by either langauge type



[pointer type]:     ../pointer-types.md
[provenence]:       ../pointer-types.md#provenence-
[sequence types]:   #signed-types "Placeholder"
[type layout]:      ../../type-layout.md
[illegal behavior]: ../../../illegal-behavior.md#integer-types-