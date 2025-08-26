# Reference patterns
```
<reference-pattern> := '&' [ 'mut' ] <pattern-no-range>
```

Reference patterns is used to derefence pointers and references, and thus, borrowing them.

Matching a value of type `&T` to a pattern like `&val` is equivalent to first dereferencing it and 

By adding `mut`, it is possible to dereference any mutable reference or pointer.

Reference patterns are always irrifutable.
