# Operator expressions
```
<op-expr> := <prefix-op> <expr>
           | <expr> <postfix-op>
           | <expr> <infix-op> <expr>
```

An operator expressions applies an operator on 1 or 2 sub-expressions.
The exact effect of an operator depends on the implementation of the operator for the 2 given types.

Operators are special from a syntax perspective, as they are one of the rare occasions where whitespace has an effect on how they are interpreted.
Specifically, the occurance of whitespace or not is what determines the exact parsing of the operators.

Unary operators must be directly attached to the expression they affect.
If multiple unary operators must be applied to an expression, each operator must be nested wihin a [parenthesized expression], e.g. chaining 2 `-` prefix operators must be written the following way: `-(-val)`, as `--val` would be applying the `--` operator instead.

Infix operators depends on the exact location between 2 expression.
Both operands of the expressions must either both be attached to the operator, e.g. `a+b`, or both be seperated from the operator, e.g. `a + b`.
The expression `a+ b` will be interpreted as the postfix operator `+` applied to `a`, followed by the expression `b`.

More information about operators can be found in their [section].

> _Note_: It is generally preferred to always have an infix separated from its operands



[parenthesized expression]: ./paren-expressions.md
[section]:                  ../operators.md