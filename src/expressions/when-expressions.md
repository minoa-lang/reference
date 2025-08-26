# When expressions
```
<when-expression> := 'when' <expr> <block> [ 'else' <expr-with-block> ]
```

A `when` expression is similar to an if expressions, but comes with 2 fundamental differences
- The condition needs to be compile time expression
- The when expression does not produce a new scope, instead the content will be placed in the surrounding scope.

The `else` can only be followed by a block, or another `when` expression.

This can be thought of as containing code marked with the cfg attribute.
