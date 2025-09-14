# Builtin types
```
<builtin-type> := <boolean-type>
                | <integer-type>
                | <floating-point-type>
                | <character-type>
                | <unit-type>
                | <never-type>
                | <type-type>
                | <opaque-type>
                | <raw-function-type>
```

Builtin types are types that are the fundamental type building blocks that do not depend on any other sub-type.

Builtin types are themselves split up into 2 kinds:
- primitive types
- compiler types

This determines how they are handled at a compiler level and how they will be represented within the compiled source

## Primitive types

A primitive type is a type which can map directly to hardware (but is not guaranteed to).

This also means that they can be represented without any abstraction when in more low-level representation of code.

The following types are primitive types:
- [boolean types]
- [integer types]
- [floating point types]
- [character types]

> _Note_: Primitive types are susceptible to [illegal behavior].

> _Todo_: Support for different endianess in types

## Compiler types

Unlike primitive types, these builtin types do not map directly to hardware.

These instead are handled specially by the compiler, while still having a distinct meaning, unlike [abstract types].

The following types are compiler types:
- [unit types]
- [never types]
- [`type` type]
- [opaque types]
- [raw function types]



[abstract types]:       ./abstract-types.md
[boolean types]:        ./builtin-types/boolean-types.md
[integer types]:        ./builtin-types/integer-types.md
[floating point types]: ./builtin-types/floating-point-types.md
[character types]:      ./builtin-types/character-types.md
[never types]:          ./builtin-types/never-types.md
[opaque types]:         ./builtin-types/opaque-types.md
[raw function types]:   ./builtin-types/raw-function-types.md
[`type` type]:          ./builtin-types/type-type.md
[unit types]:           ./builtin-types/unit-type.md
[illegal behavior]:     ../../illegal-behavior.md
