# Throw expressions
```
<throw-expr> := `throw` <expr>
```

A `throw` expression does 2 things:
- it acts like syntactic for returning of a value with erronous-supportred type, e.g. `Result[_, _].Err(...)`
- it triggers any [error defer] that are current in-scope to be evaluated

It can therefore be compares to explicitly [returning] an erronous value, but this would not trigger any of the error defers.

To support the `throw` expression on custom type, the 

> _Note_: Unlike languages with exceptions, this expression does not throw an exception

> _Example_
> ```
> fn foo() -> i32!i32 {
>     errdefer println("hit error");
> 
> 
>     // does not return `.Ok(3)`, but instead `.Err(3)`
>     // additionally, it will also cause the `errdefer` to run
>     throw 3;
> }
> ```



[returning]:    ./return-expressions.md
[error defer]:  ../statements/defer-statements.md#error-defer

[`@throwable`]: ../attributes.md "Todo: link to correct attribute"