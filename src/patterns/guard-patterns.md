# Guard patterns
```
<guard-pattern> := <pattern> 'if' <expr>
```

Patterns can accept guard patterns to further refine the cirteria for matching a pattern.P
Pattern guards apread after the pattern a consists out of a boolean expression.

When the pattern matches successfully, the pattern guard expression is executed.
If the expression evaluates to `true`, the pattern is successfully matched against.
Otherwise, the next pattern including other alternative patterns with the `|` operator in the same pattern are tested..

A pattern guard may refer to the variable bound within the pattern they follow.
Before evaluating the guard, a shared reference to the scrutinee is taken, and is then used when accessing the variable.
Only when the guard evaluates to `true` is the value moved, or copied without moving out of the scrutinee in case the guard fails to match.
Moreover, by holding a shared reference while evaluating the guard, mutation inside the guard is also prevented.

Any bound values within the guard cannot be used outside of it.

If a `|` is encountered in the expression, it will be interpreted as part of an alternative pattern.
If a `|` is required in the expression, it should be wrapped in a [paranthesized expression]

Guard patterns are refutable.



[paranthesized expression]: ../expressions/paren-expressions.md