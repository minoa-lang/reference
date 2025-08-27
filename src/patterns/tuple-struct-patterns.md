# Tuple struct patterns
```
<tuple-struct-pattern>      := [ <path> | '.' ] '(' ( <tuple-pattern-elem> { ',' <tuple-pattern-elem> }* [ ',' ] ) | <rest-pattern> ')'
<tuple-struct-pattern-elem> := [ <name> ':' ] <pattern>
```

A tuple struct pattern can match tuple structs that match the defined criteria in the subpatterns.
They also allow for a tuple struct or enum value to be destructured into its fields.

Tuple struct pattern can also have an inferred path by starting it with a '.'

Only a single [rest pattern] is allowed within a tuple struct pattern.

A tuple struct pattern is refutable if the pattern either refers to an variant or an enum with more than 1 variant, or if any of its subpatterns is refutable.



[rest pattern]: ./rest-patterns.md