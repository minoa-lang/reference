# Catch expressions
```
<catch-expr> := <expr> 'catch' '(' <name> ')' <expr>
```

A catch expression calculate the value proceeding it first, and if the returning value would be a valid value, that value is returned.
If the returning value is an erronous value, the catch' body will be executed, which captures the error value an can be used by the expression following it

To decide wether the the left-hand value is an 'erronous' value, the `Catch` trait is used

This is shorthand for:
```
if val = (expr).(Catch.check)()! {
    val
} else (err) {
    // ...
};
```

For a non-error capturing equivalent, use the corresponding [`??` operator].


[`??` operator]: ../operators/special-operators.md#catch-operator-