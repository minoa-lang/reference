# Operator expressions
```
<op-expr> := <prefix-op> <expr>
           | <expr> <postfix-op>
           | <expr> <infix-op> <expr>
```


An operator expression applies operators on 1 or 2 sub-expressions.
The resulting value of these expression will depend on the implementation of the operators.

When calling a prefix of postfix operator, the operator needs to be directly next to the expression it applies to and may not be separated by space.
When it comes to infix operators, they may be placed between sub-expressions without spaces, as this means there there is not pre- or postfix expression within the either operand.
Otherwise, if a post or prefix expression must be used, it must not be directly placed against the another expression, but must be separated with a space.

Prefix and postfix operators can only chained when the by using parenthesized expression, meaning that chaining 2 `-`s requires the following to be written: `-(-val)`.

For additional info on operators, check the [Operator section].

> _Note_: Is it generally preferred to have spaces around infix operators regardless of when a prefix of postfix expression is on either side.



[Operator section]: ../operators.md