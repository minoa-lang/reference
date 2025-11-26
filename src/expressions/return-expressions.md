# Return expressions
```
<return-expr> := 'return' [ <expr> ]
```

Return expressions allow for explicit exits from a function, allowing the function to destroy its current function activation frame, and transfer control back to teh caller fame.

A `return` may also be provided with a value, this will then place this value in the location defined by the function signature, so it can be passeed to the caller.

A [comma expression] can be provided to a return expression, which will then be implicitly produce an [tuple] value.

> _Example_
> ```
> fn foo() {
>     if cond() {
>         // conditional early out, immediatally exists the function
>         return;
>     }
> 
>     println("regular return");
> 
>     // explicitly return from an expression
>     return;
> }
> 
> fn bar() -> i32 {
>     // return an explicit value
>     return 42;
> }
> 
> fn baz() -> (i32, f32) {
>     // automatically warps the provided values to produce a tuple
>     return 2, 3.0;
> }
> ```

# Return in generator funcitons [↵](#return-expressions)

When a `return` is located within a [generator function], it will act similarly as if it were located within a regular function.
Meaning that it will exit the function, but also end the generation of new values, i.e the generator function cannot generate new values after it.

To return a value from a generator function without terminating the generation, a [`yield` expression] can be used.


[comma expression]:   ./comma-expressions.md
[`yield` expression]: ./yield-expressions.md
[generator function]: ../items/functions.md#generator-functions-
[tuple]:              ../type-system/types/composite-types/tuple-types.md