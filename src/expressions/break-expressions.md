# Break expressions
```
<break-expr> := 'break' [ <label> ] [ <expr> ]
```

When `break` is encountered:
- in a loop, execution of the associated loop body is immediatelly terminated.
- in a `match`, execution of the associated arm is immediatelly terminated.

A `break` expression is normaly associated with the innermost loop or `match` exclosing the `break` expression, but a label can be used to specify which enclosing loop or `match` is affected.

A `break` expression is only permited in the body of a loop, or an arm of a `match`.

### 9.21.1. Break and loop/match values [↵](#break-expressions)

When associated with a loop, a break expression may additionally return a value from that loop.
`break` without an explicit expression is considered identical to a `break` with the expression `()`.
