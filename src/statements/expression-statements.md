# Expression statements
```
<expr-stmt> := <expr-no-block> ';'
             | <expr-with-block>
```

An expressions statement evaluates a given expression and ignores the result.
As a rule, an expression statement's purpose is to trigger the effects of evaluating its expression.

If an expression contains a block, and if used in a context where a statement is permitted, the trailing semicolon can be omitted.
This could lead to ambiguity, when this can be parsed as both part of a larger expression or as a standalone expression, it will be parsed as a statement, unless it is the last value of a block.

When an expression containin a block is used as a statement, it must return the [unit type].



[unit type]: ../type-system/types/builtin-types/unit-types.md