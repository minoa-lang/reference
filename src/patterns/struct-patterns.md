# Struct patterns
```
<struct-pattern>             := ( <path> | '.' ) '{' [ ( <struct-pattern-elem> { ',' <struct-pattern-elem> }* [ ',' [ <rest-pattern> ] ] ) | <rest-pattern> ] '}'
<struct-pattern-elem>        := { <attribute> }* ( <struct-pattern-elem-tuple> | <struct-pattern-elem-member> | <struct-pattern-elem-iden> )
<struct-pattern-elem-tuple>  := <tuple-elem-idx> ':' <pattern>
<struct-pattern-elem-member> := <name> ':' pattern
<struct-pattern-elem-iden>   := [ 'ref' ] [ 'mut' ] <name> [ '@' <pattern> ]
<struct-pattern-etc>         := '..'
```

A struct pattern can match struct, enum, and union values that match the defined criteria in the subpatterns.
The also allow for the value to be deconstructed to its members.

Struct pattern can also have an inferred path by starting it with a '.'

There are 3 ways of matching elements:
- Using a tuple element in case of tuple-like types
- Using a values name, followed by a pattern
- Using a value directly with a matching name, which may also include a bound pattern.

A structure's field may be ignored by using a `..`, which is similar to a rest pattern, but is distinct in its meaning.
If a `..` is not used, the struct patterns is required to refernce all fields.

A struct pattern used to match a union must have exactly one field (see [pattern matching on unions](../type-system/types/union-types.md#pattern-matching-on-unions-)

When a field is directly used, it acts like a [identifier pattern](./identifier-patterns.md).

A struct apttern is refutable if the pattern either refers to an variant or an enum with more than 1 variant, or if any of its subpatterns is refutable.
