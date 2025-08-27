# Defer statements
```
<defer-stmt> := 'defer' <expr-with-block>
              | `defer` <expr-no-block> ';'
<err-defer-stmt> := 'errdefer' [ <err-defer-capture> ] <expr-with-block>
                  | `errdefer` [ <err-defer-capture> ] <expr-no-block> ';'
<err-defer-capture> := '|' [ 'mut' ] <name> '|'
```

A defer expressions delays the execution of an expression until the end of the scope, but before any variables are being dropped.
Defers are evaluated in the reverse order they are called, in a so-called LIFO (Last-In First-Out) order.

## Defer-on-error statement [↵](#defer-statements)

A defer-on-error statement is a variation of a defer statement.
Unlike a normal defer statement, it only defers when the function is returned by either a [catch operator] or a [throw expression].
Defer-on-error will only be evaluated if the error defer is in the current scope, meaning that if a scope is exited, the defer-on-error will not be executed when one of the above expressions cause an error.

Evaluating error defers can be avoided by explicitly returning an erronous value.

A defer-on-error statement can also capture a reference or mutable reference of the resulting error to be used inside of the error defer's body.



[throw expression]: ../expressions/throw-expressions.md
[catch operator]:   ../operators/special-operators.md#catch-operator-