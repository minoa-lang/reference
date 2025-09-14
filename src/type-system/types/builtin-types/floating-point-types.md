# Floating point types
```
<floating-point-type> := \f(16|32|64|128)[le|be]\
```

A floating point type represent the same sized type as defined in the IEEE-754-2008 specification.

Below is a table of supported floating-point types:

Type   | Bit width | Mantissa bits      | Exponent bits | Min value  | Max value   | Smallest value | Significant decimal digits | Notes
-------|-----------|--------------------|---------------|------------|-------------|----------------|----------------------------|------
`f16`  | 16-bits   | 10 (11 implicit)   | 5             | 6.55e+5    | -6.55e+5    | 6.10e-5        | 3                          |
`f32`  | 32-bits   | 23 (24 implicit)   | 8             | 3.40e+38   | -3.40e+38   | 1.17e-38       | 6                          |
`f64`  | 64-bits   | 52 (53 implicit)   | 11            | 1.80e+308  | -1.80e+308  | 2.23e-308      | 15                         |
`f80`  | 80-bits   | 1 + 63             | 15            | 1.19e+4932 | -1.19e+4932 | 3.36e-4932     | 18                         | Does not have implicit bit, but explicit integer bit, i.e. `1 + ...`
`f128` | 128-bits  | 112 (113 implicit) | 15            | 1.19e+4932 | -1.19e+4932 | 3.36e-4932     | 33                         |

Both the size and alignment of the floating points are defined by their bit-width.

Most commonly, only `f32` and `f64` are implemented in hardware and have native instructions (like `f80` only being on x86), if any type does not have native instructions, the program will fall back to 'emulating' these types.

Minoa represents floating point numbers as defined in IEEE-754-2019.
Exact rules on how floating point types are handled in calculation depends on the corresponding [floating point control settings] within the scope.

> _Note_: Unlike iteger types, a floating point type does not support an arbitrary bitwidth, as IEEE-754 does not provide a mechanism for this.

> _Todo_: Other floating point types may be added in the future.

## Endianess

Like [integer types], floating point types support a specific endianness to be provided.
This can be done by appending either `le` or `be` to the end of the type, specifying little- and big-endian respectively.

These types have similar properties to their versions without an explicit endianess in terms of size and aligment, but have bytes that are laid out in the specific endianness.
In addition, these types don't allow any operator to be called on them and must first be converted to their native/machine-endian versions.

> _Note_: Trait implementations may still be provided for these types, but none of the core operators are implemented, as this might require intermediate conversions to native/machine-endianness, which might be unclear to the user, as they could expect all integer types to behave the same in compiled code.

## Language item [↵](#floating-point-types)

Floating points are represented by the langauge as
```
@builtin(floatN)
struct FloatN[E: Endianness];
```
where `N` is replaced by the bitwidth of the float.



[integer types]:                   ./integer-types.md
[floating point control settings]: ../../../attributes/code-generation.md#fp_control-