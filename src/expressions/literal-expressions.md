# Literal expressions
```
<lit-expr> := <literal> [ <name> ]
```

A literal expression constists out of a literal token (or multiple, in case of multi-line strings), which denotes the value it will evaluate to.
They can be seen as a constant which is defined inline, and is primarily, but not always, executed at compile-time.

In addition, an optional literal operator may be provided after the literal.
This allows the literal to be evaluated to different types either at compile-time, in case of a fixed size type, but may also evaluate at runtime, e.g. when needing to allocate backing memory.
Literal operators may also restrict the possible values that can be used to represent the given type.

> _Note_: For more info about the functionality of literal operators, see their [relavent section].

> _Note_: Literal operators cannot be confused with [extended names], as they do not share any location where both values are allowed.

## Implicit literal types

Each literal defined within the [literals section] has its own literal representation which, unless explicitly declares, will not be the type of the literal expression.

In cases where no explicit literal operator is defined, the compiler will pick the most well suited type for the literal.
If a primitive type is explicitly defined, the literal will coerce into the given type, if it is allowed as defined in the below table

Literal kind         | Allowed types
---------------------|---------------------------------------------
Integral decimal     | [signed] and [unsigned] integers
Float decimal        | [floating point]
Binary               | [signed] and [unsigned] integers
Octal                | [signed] and [unsigned] integers
Integral hexadecimal | [signed] and [unsigned] integers
Float hexadecimal    | [floating point]
Boolean              | [booleans]
Character            | [character]
String               | [string slices]

Otherwise, if no explicit builtin type is defined, the compiler will default to type defined below

Literal kind         | Default type
---------------------|---------------------------------------------------
Integral decimal     | `i32` if a value fits whithin it, `i64` otherwise
Float decimal        | `f64`
Binary               | `u32` if a value fits whithin it, `u64` otherwise
Octal                | `u32` if a value fits whithin it, `u64` otherwise
Integral hexadecimal | `u32` if a value fits whithin it, `u64` otherwise
Float hexadecimal    | `f64`
Boolean              | `bool`
Character            | `char`
String               | `&str`



[relavent section]:  ../operators/literal-operators.md
[extended names]:    ../lexical-structure/names.md#extended-names-
[Literals section]:  ../literals.md
[boolean]:           ../type-system/types/builtin-types/boolean-types.md
[character]:         ../type-system/types/builtin-types/character-types.md
[gloating point]:    ../type-system/types/builtin-types/floating-point-types.md
[signed]:            ../type-system/types/builtin-types/integer-types.md#signed-types-
[unsigned]:          ../type-system/types/builtin-types/integer-types.md#unsigned-integer-types-
[string slices]:     ../type-system/types/sequence-types/string-array-slice-types.md