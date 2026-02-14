# Boolean types
```
<boolean-type> := 'bool' | ? b{N}, where N is any integer <= 65536 ?
```

A boolean type is a primitive type that can be used to define 1 out of 2 possible states: `true` or `false`.
As a boolean only can represent these 2 values, there are also only 2 valid bit representations for a boolean.
These are `0x0` and `0x1`, meaning that the lower bit is set to the value, and all other bits are set to 0.
Any other bitpattern is [illegal behavior].

Boolean types can be represented in 2 ways, as a basic `bool` type, or a boolean type with a specific width.

When defined as a `bool`, it generally has a size and alignment of 1 byte.
But when located inside of a bitfield, it represents a 1 bit type.

Meanwhile when defined in the form `b{N}`, the boolean is defined with a specific bit-width.
Outside of a bitfield, it takes up the minimum amount of bytes required to represent the given bitwidth.
But when located inside of a bitfield, it represents a type with the exact number of bits defined within the type.

It is generally recommended to use the `bool` type whenever possible, while specifically sized booleans should only be used in a context where exact control of the bitsize is required.
Specifically variably sized booleans should only be used in the following situations:
- using a `b8` within a bitfield, to ensure the bitfield will always take up a full byte
- when being mapped to a boolean type used by an FFI interface, e.g. `b32` as an equivalent to a Win32 `BOOL`.

It is not possible to implement any items on a partial/generic subset of boolean types, it is only possible to implement on:
- `bool`: this will result in the operation to be implemented on all boolean types
- a specific width boolean: only implementing an item for this specific type

> _Note_: Unlike [integer types], variably-sized boolean types do not support endianess
_: Minoa represents signed integer types using two's complement

### Endianness [↵](#integer-types)

Boolean type additionally support a specific endianess to be provided.
This can be done by appending either `le` or `be` to the end of the type, specifying little- and big-endian respectively.

These types have similar properties to their versions without an explicit endianess in terms of size and aligment, but have bytes that are laid out in the specific endianness.
This is to prevent issues where endianess might result in invalid boolean representations when using multi-byte booleans, such as a `b16` being interpreted as `0x0100` instead of `0x0001`.

In addition, these types don't allow any operator to be called on them and must first be converted to their native/machine-endian versions.

> _Note_: Trait implementations may still be provided for these types, but none of the core operators are implemented, as this might require intermediate conversions to native/machine-endianness, which might be unclear to the user, as they could expect all integer types to behave the same in compiled code.



[integer types]:    ./integer-types.md
[illegal behavior]: ../../../illegal-behavior.md#boolean-types-