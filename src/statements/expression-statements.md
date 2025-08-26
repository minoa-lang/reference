# Expression statements
```
<expr-stmt> := <expr-no-block> ';'
             | <expr-with-block>
```

An expressions statement evaluates a given expression and ignores the result.
As a rule, an expression statement's purpose is to trigger the effects of evaluating its expression.

If an expression ends with a block, and if used in a context where a statement is permitted, the trailing semicolon can be omitted.
This could lead to ambiguity, when this can be parsed as both part of a larger expression or as a standalone expression, it will be parsed as a statement.

The implicit return type of a statement at the end of a function is a unit type.
