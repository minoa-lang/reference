# Tuple patterns
```
<tuple-pattern>      := '(' ( <tuple-pattern-elem> { ',' <tuple-pattern-elem> }* [ ',' ] ) | <rest-pattern> ')'
<tuple-pattern-elem> := [ <tuple-elem-idx> ':' ] <pattern>
```

A tuple pattern can match a tuple values that match the defined criteria in the subpatterns.
They also allow for a tuple to be destructured into its fields.

Only a single [rest pattern](./rest-patterns.md) is allowed within a tuple pattern.

The form `(..)` is a special form of a tuple patterns, which matches a tuple of any size.
This patterns is always irrifutable.

A tuple with a single aptterns is a grouped pattern.

A tuple pattern is refutable if any of its fields are refutable.
