# Parenthesized expressions
```
<paren-expr> := '(' <expr> ')'
```

A parenthesized expression, also known as a grouped expression, wraps a single expression, allowing the expression to be evaluated before any other expressions that are outside of the parentheses will be executed.

Parenthesized expressions can be both place and value expressions, depending on the expression within parentheses.

Parentheses explicitly increase the precedence of this expression above that of other expressions, allowing expressions that would have a lower precedence to be executed before outer expressions use this expression.
