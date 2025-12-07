# Await expressions
```
<await-expr> := `await` [ <expr> ]
```

An `await` expression allows for the calling of one of the following:
- an [`async` function]
- an [`async` block]
- an [`async` block]
- any type implementing an async call trait:
  - _Todo_: Async call traits

This will also act as a syncing point for the async context it is located in.

An `await` expression is only allowed within an async context.

_Example_
```
async fn foo() { ... }

async fn bar() {
    await foo();
}
```

[`async` block]:    ./block-expressions.md#async-block-
[`async` closure]:  ./closure-expressions.md#async-closures-
[`async` function]: ../items/functions.md#async-functions-

