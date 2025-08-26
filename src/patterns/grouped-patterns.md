# Grouped patterns
```
<grouped-pattern> := '(' <pattern> ')'
```

Grouped patterns are used to explicitly control the precedence of compound patterns.

An example would be `&0..=5`, which is ambiguous and not allowed, this can be solved by instead writing `&(0..=5)`, as `&0` is not allowed in a range.

The form `(..)` is not a grouped pattern, but instead a [tuple pattern](./tuple-patterns.md)

A gouped pattern is rufutable if it subpattern is refutable.
