# Literal expressions
```
<lit-expr> := ? <literal> except <int-decimal-literal> ? [ [ "'" ] <name> ]
            | <int-decimal-literal> [ "'" <name> ]
```
A literal expression consists of a literal token (or multiple in case of multi-line strings), that denotes the value it will evaluate to.
It is similar to a constant value, and is (primarily) evaluated at compile time.

In addition, literal expression can also invoke a literal operator to be applied of them, which can either be evaluated at compile time, in case of a fixed size type, but may also evaluate at runtime, for example when needing to allocate backing memory.
Literal operators may have an effect on which values the contents are allowed to be.

In most cases, the literal operator can be applied by directly appending it to the end of the literal.
The integer decimal literal is an exception, as `3m` will be interepreted as a name and not as a literal `3`, followed by a literal operator.
In this case, a `'` (single quote) can be used, resulting in the example being `3'm`, this can be done as 2 literals can never follow each other.

A `'` (single quote) is allowed for other literals, but it not neccesary.

If a signle quote is used, it must be directly attached to the literal and may not have any whitespace.

## Literal type conversion [↵](#literal-expression)

Each literal defined within the [Literals section](../literals.md) has its own literal representation, which itself cannot be the type of the literal expression.
Therefore each literal needs to be converted to a type that can be used.

If a literal operator is used, this is fairly simple, as the value will be of the type returned by the literal operator.

If none is used, the compiler needs to figure out the best possible type. If this can be derived from the surrounding environment, the value will be implicitly converted.
Each kind of literal can be converted into a set of type defined below:

Literal kind                 | Possible types
-----------------------------|----------------
Integral decimal literal     | [Signed integers] and [unsigned integers]
Float decimal literal        | [Floating point]
Binary literal               | [Signed integers] and [Unsigned integers]
Octal literal                | [Signed integers] and [Unsigned integers]
Integral hexadecimal literal | [Signed integers] and [Unsigned integers]
Float hexadecimal literal    | [Floating point]
Boolean literal              | [Booleans](../type-system/types/primitive-types.md##boolean-types)
Character literal            | [Characters](../type-system/types/primitive-types.md##character-types)
String literal               | [String slices](../type-system/types/string-slice-type.md)

In case the compiler cannot figure out the type from surrounding context, it will default to the following:

Literal kind                 | Default types
-----------------------------|----------------
Integral decimal literal     | `i64`
Float decimal literal        | `f64`
Binary literal               | `u64`
Octal literal                | `u64`
Integral hexadecimal literal | `u64`
Float hexadecimal literal    | `f64`
Boolean literal              | `bool`
Character literal            | `char`
String literal               | `str`



[Literals section]:  ../literals.md)
[Characters]:        ../type-system/types/primitive-types.md##character-types
[Booleans]:          ../type-system/types/primitive-types.md##boolean-types
[Floating point]:    ../type-system/types/primitive-types.md#floating-point-types
[Signed integers]:   ../type-system/types/primitive-types.md#signed-types
[unsigned integers]: ../type-system/types/primitive-types.md#unsigned-types
[String slices]:     ../type-system/types/string-slice-type.md