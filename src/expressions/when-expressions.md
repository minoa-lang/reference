# When expressions
```
<when-expr> := 'when' <scrutinee> <block> [ 'else' <expr-with-block> ]
<when-expr-else> := <block>
                  | <when-expr>
                  | <if-expr>
```

A `when` expression is the expression version of a [`when` item], which can be located where expressions are allowed.

For most purposes, each arm of a when statement counts as [block expression], meaning it may contain statements.
If the `when` expression is located in a place where a statement is allowed, and no final expression is provided, it will be interpreted a [`when` statement].

Since this version is allowed within any expression, it is not allowed to contain any statements, and may only contain a single expression.
If the `when` is located where both statements and expressions are allowed, it will be interpreted as a [`when` statement].

_Example_
```
a := when #target_os == .windows { 1 } else { 2 };

b := when #target_os == .linux {
        3
    } else if a == 1 {
        4
    } else {
        5
    };
```


[block expression]: ./block-expressions.md
[`when` item]:      ../items/when-items.md
[`when` statement]: ../statements/when-statements.md