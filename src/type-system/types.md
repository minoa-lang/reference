# Types

```
<type>           := <type-no-bound>
                  | <trait-type>
<type-no-bound>  := <builtin-type>
                  | <sequence-type>
                  | <pointer-like-type>
                  | <function-like-type>
                  | <aggregate-type>
                  | <abstract-type>

                  | <parenthesized-type>
                  | <path-type>
                  | <tuple-type>
                  | <array-type>
                  | <slice-type>
                  | <string-slice-type>
                  | <pointer-type>
                  | <reference-type>
                  | <optional-type>
                  | <function-type>
                  | <function-pointer-type>
                  | <closure-type>
                  | <record-type>
                  | <enum-record-type>
                  | <inferred-type>
```

Types are an essential part of any program, each variable, value, and item has a type.
The type defines how a value is interpreted in memory and what operations can be performed using them.

Some types support unique functionality that cannot be replicated using user defined types.

While types are generally split up in 2 categories: _builtin_ and _compound_ types, the latter is subdivided within multiple groups.
These are the following:
- [builtin types]
  - [boolean types]
  - [integer types]
  - [floating point types]
  - [character types]
  - [unit types]
  - [never types]
  - [`type` types]
  - [opaque types]
- [compound types]
  - [sequence types]
    - [array types]
    - [slice types]
    - [strings slice types]
  - [pointer-like types]
    - [pointer types]
    - [reference types]
    - [function pointer types]
  - [function-like types]
    - [function types]
    - [closure types]
  - [aggregate types]
    - [tuple types]
    - [struct types]
    - [tuple struct types]
    - [enum types]
    - [union types]
    - [bitfield types]
  - [trait types]
    - [impl trait types]
    - [trait object types]
  - [abstract types]
    - [path types]
    - [optional types]
    - [result types]
    - [`Self` types]
    - [vector types]
    - [infered types]

> _Note_: types are designed to be able to be easily read from left to right

## Compound types [↵](#types)

A compound type is a type that requires one or more sub-types to have a meaning.
This mean it is used to build a type upon any other type.

## Type expressions [↵](#types)
```
<type-expr> := <type>
```

Types themselves are expressions of the special [`type` type].
Meaning that they can therefore both be used in locations that accept either types of expressions.

This allows them to be used wherever an expression can be used

## Parenthesized types [↵](#types)
```
<parenthesized-type> := '(' <type> ')'
```

In some locations it may be possible that a type would be ambiguous, this can be solved using a parenthesized type.
For example, a reference to an trait object type with multiple bounds can be unclear, as we cannot cleanly determine if one of the bounds is a reference,
or the whole set of bounds constitute a single type without requiring to rely heavily on context.

Example:
```
&dyn Foo & Sized
```
may either be interpreted as a trait object with 2 bounds, or a reference located within a binary AND expression.
Because of this, it requires the following 'clarification':
```
&dyn (Foo & Sized)
```

## Recursive types [↵](#types)

Compound types may be recursive, meaning that a type may have member that refers, directly or indirectly, to the current type, before hitting a _terminal_ type, this ensure the type will have a finite size.
_Terminal_ types indicate a point where the size of a type is not dependent on one of its subtypes.
They are on of the following:
- [builtin types]
- [pointer types]
- [reference types]
- [function pointer types]

In addition, any [type alias] may not depend on itself within it definition, either directly, or indirectly via another type alias, even when done via a _terminal_ type.


[compound types]:         #compound-types-
[abstract types]:         ./types/abstract-types.md
[infered types]:          ./types/abstract-types/inferred-types.md
[optional types]:         ./types/abstract-types/optional-types.md
[path types]:             ./types/abstract-types/path-types.md
[result types]:           ./types/abstract-types/result-types.md
[`Self` types]:           ./types/abstract-types/self-type.md
[vector types]:           ./types/abstract-types/vector-types.md
[aggregate types]:        ./types/aggregate-types.md
[bitfield types]:         ./types/aggregate-types/bitfield-types.md
[enum types]:             ./types/aggregate-types/enum-types.md
[struct types]:           ./types/aggregate-types/struct-types.md
[tuple struct types]:     ./types/aggregate-types/tuple-struct-types.md
[tuple types]:            ./types/aggregate-types/tuple-types.md
[union types]:            ./types/aggregate-types/union-types.md
[builtin types]:          ./types/builtin-types.md
[boolean types]:          ./types/builtin-types/boolean-types.md
[character types]:        ./types/builtin-types/character-types.md
[floating point types]:   ./types/builtin-types/floating-point-types.md
[integer types]:          ./types/builtin-types/integer-types.md
[never types]:            ./types/builtin-types/never-type.md
[opaque types]:           ./types/builtin-types/opaque-types.md
[`type` types]:           ./types/builtin-types/type-type.md
[`type` type]:            ./types/builtin-types/type-type.md
[unit types]:             ./types/builtin-types/unit-type.md
[function-like types]:    ./types/function-like-types.md
[closure types]:          ./types/function-like-types/closure-types.md
[function types]:         ./types/function-like-types/function-types.md
[pointer-like types]:     ./types/pointer-like-types.md
[function pointer types]: ./types/pointer-like-types/function-pointer-types.md
[pointer types]:          ./types/pointer-like-types/pointer-types.md
[reference types]:        ./types/pointer-like-types/reference-types.md
[sequence types]:         ./types/sequence-types.md
[array types]:            ./types/sequence-types/array-types.md
[slice types]:            ./types/sequence-types/slice-types.md
[strings slice types]:    ./types/sequence-types/string-slice-type.md
[trait types]:            ./types/trait-types.md
[impl trait types]:       ./types/trait-types/impl-trait-types.md
[trait object types]:     ./types/trait-types/trait-object-types.md
[type alias]:             ../items/type-aliases.md