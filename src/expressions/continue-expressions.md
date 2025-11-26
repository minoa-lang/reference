# Continue expressions
```
<continue-expr> := 'continue' [ <label> ]
                 | `continue` [ <label> ] <expr>
```

The behavior of a `continue` expression depends on the surrounding expression.

# Loop `continue`

When in a [loop expression], it will cause the loop to directly go to the loop head for its next iteration, skipping the rest of the current body.

Depending on the loop expression, this is loop head is one of the following:
- [`loop`]: the start of the loop body
- [`while`]: the increment if it exists, otherwise the condition check
- [`do`-`while`]: the condition check
- [`for`]: the iterable's call for the next value

> _Example_
> ```
> while cond() {
>     if calc() == 1 {
>         continue;
>     }
> 
>     println("ignoring the continue")
> }
> ```

Additionally, this `continue` variant may also be provided with a label to the loop that needs to be exited

> _Example_
> ```
> :label: loop {
>     while cond() {
>         // will exit the outer loop
>         break :label;
>     }
> }
> ```

# `match` `continue`

When in a [`match`] expression, it will immediatally re-evaluate the `match` expression and execute the resulting arm.

> _Example_
> ```
> enum Foo { A(i32), B(i32), C(i32) }
> 
> match Foo.A(1) {
>     .A(val) => {
>         // will re-evaluate the match, but with the provided value
>         continue Foo.B(3);
>     },
>     .B(val) => {
>         continue Foo.C(42);
>     },
>     .C(val) => {
> 
>     }
> }
> ```

This can be seen as if the `match` were located within a loop, and where all arms without a `continue` result in a break.

> _Example_
> ```
> mut scrutinee := Foo.A(1);
> 
> :outer: loop {
>     match scrutinee {
>         .A(val) => {
>             scrutinuee = Foo.B(3);
>         },
>         .B(val) => {
>             scrutinuee = Foo.C(42);
>         },
>         .C(val) => {
>             break :outer;
>         }
>     }
> }
> ```

In addition, when the pattern matching is simple enough, the compiler can optimize this code by putting a jump table at the end of each branch.
This allows for a direct jump to the correct branch and skips the overhead of jumping to the `match`'s head before being able to jump to the correct branch.
This also aids with better branch prediction.
If the compiler is able to gifure out to which case to jump at compile time, this would become a direct jump to this branch and will just bind any bindings.

An example of a usecase where this can be very beneficial would be a state machine.


[loop expression]: ./loop-expressions.md
[`loop`]:          ./loop-expressions/inf-loop-expressions.md
[`while`]:         ./loop-expressions/while-expressions.md
[`do`-`while`]:    ./loop-expressions/do-while-expressions.md
[`for`]:           ./loop-expressions/for-expressions.md
[`match`]:         ./match-expressions.md
