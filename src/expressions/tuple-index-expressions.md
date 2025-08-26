# Tuple index expressions
```
<tuple-index-expr> := <expr> [ '?' ] '.' <int-decimal-literal>
```

A tuple index expressions is used to access fields within a tuple type (a tuple or tuple structure).

A tuple is indexed using an unsigned decimal literal, with no leading zeros or underscores.

Evaluation of a tuple has no side-effects, other than the evaluation of the tuple operand.
This expressions is a place expression, so it evaluates to the location of tuple field with the same name as the tuple index.

Similar to a [field access expression](./field-access-expressions.md), tuple index expressions also support `?.` (null-propagating) accesses.
The indexing will only happen if the value it is called on is a non 'err' value, otherwise it will propagate this value.
The `?.` access is supported on any type that implements the `OptAccess` trait.

> _Note_: Named tuples are indexed using a [field access expression](./field-access-expressions.md)
