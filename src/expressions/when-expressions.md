# When expressions
```
<when--expr> := 'when' <expr> <when-expr-body> [ 'else' <expr-with-block> ]
<when-expr-body> := '{' <expr> '}'
<when-expr-else> := <when-expr-body>
                  | ? <expr-with-block>, except <block-expr> ?
```

A `when` expression is the expression version of a [`when` item], which can be located where expressions are allowed.

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


[`when` item]:      ../items/when-items.md
[`when` statement]: ../statements/when-statements.md