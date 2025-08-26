# Guard statements
```
<guard-stmt> := 'if' <expr> 'else' <block>
```

A guard statement is similar to an [`if` statement](#918-if-expression-), but does the opposite.
A guard statement only has an else block, which defines what should happen when the expression results in a `false` value.
Since they do not have a regular body, they are not counted as expressions

> _Note_: the reason this is a statement and not an expression, is because unlike an `if`, a `guard if` cannot return a value, since it only executes on a `false` value
