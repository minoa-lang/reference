# Yield expressions
```
<yield-expr> := `yield` [ <expr> ]
```

A `yield` expression allows a value to be returned from a yield supporting context, without terminating the context it is located in, but suspending it until the next call.

The exact behavior depends on the location of the `yield` expression:
- within a [generator function], it will temporarily suspend the executiohn of the function and return a value, this function can be resumed by another subsequent call to the function.
- within a [list comprehension], it will produce a value to place within the list, and immediatelly continue execution

In any of the above cases, the yield *must* have a value.

A `yield` may also be used instead of a [`return` expression] or a final expression within [block] when returning the final value from the generator function.

> _Example_
> ```
> gen fn foo() -> i32 {
>     yield 1;
>     yield 2;
>     yield 3;
> }
> 
> // will only ever return `1`
> a := foo();
> 
> // will print all 3 values
> for val in foo() {
>     println("\{val}");
> }
> ```

When a `yield` is encountered within an `async` context, it may appear without an expression.
This will result in an additional sync point that is not associated with another `async` call, unlike an [`await` expression].

> _Example_
> ```
> async gen fn foo() -> i32 {
>     yield 1;
> 
>     // async sync point
>     yield;
> 
>     yield 2;
> }
> 
> async fn bar() -> i32 {
>     // a `yield` without a value is allowed in a non-generator async function
>     yield;
> 
>     2
> }
> ```



[`await` expression]:  ./await-expressions.md
[block]:               ./block-expressions.md
[list comprehension]:  ./list-comprehension-expressions.md
[`return` expression]: ./return-expressions.md
[generator function]:  ../items/functions.md#generator-functions-