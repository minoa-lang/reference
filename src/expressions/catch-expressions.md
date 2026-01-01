# Catch expressions
```
<catch-expr> := <expr> 'catch' '(' <name> [ ':' <type> ] ')'
```

A `catch` expression for the handling of an error within the same expression as the sub-expressions which produces the error.
The left-hand side is calculated first, and if it results in an error, only then will the right-hand side be evaluated, otherwise it is skipped and the expression propagates the left-hand side's value.

The `catch` may capture the value of the error produced, or may ignore the value.
In addition, a type may be specifies, this might however be more useful in combination with a [`try` expression].

The expression determines whether the value is erroneous using the [`Catch`] trait.

For a version of this expression that does not capture the error value, the [`??` operator] can be used.

> _Note_: The behavior of `catch` is slightly different when following a `try`, as defined [here].

> _Example_
> ```
> a: ?i32 = null;
> 
> // either produces the value of `a`, or `3` if `a` is `null`
> val := a catch 3;
> 
> b : i32!bool = .Err(42);
> 
> // either produces the value of `b`, or the result of the error being greater than 0 if `a` is stores an error
> val := a catch(err: i32) err > 0;
> ```
> 
> The `catch` expression is equivalent to the following:
> ```
> if val := (expr).(Catch.check)()? {
>     val
> } else (err) {
>     // ...
> }
> ```



[`try` expression]: ./try-expressions.md
[here]:             ./try-expressions.md#interaction-with-catch-expressions-
[`??` operator]:    ../operators/special-operators.md#catch-

[`Catch`]:          #catch-expressions "Todo: link to docs"