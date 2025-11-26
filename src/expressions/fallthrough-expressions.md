# Fallthrough expressions
```
<fallthrough-expr> := 'fallthrough' [ <label> ] [ <expr> ]
```

A `fallthrough` expression allows a [`match`] expression to continue execution of another branch after the current one, instead of immediatally exiting the `match`.

If no explicit label is supplied, the `fallthrough` will go the the branch immediatally after the current branch.
Otherwise, it will branch to the arm with the specified label.

By default, to be able to fall through into another case, that case must not have any bindings.
However, it is possible to pass values to the next arm.
This can be done in the following ways:
- if only a single binding exists, the value can directly be passed to the next arm
- if no [alternative patterns] exist that have a different ordering of bindings, the values of the bindings may be passed as a tuple
- a struct expression may be passed, with an inferred path and field which map to the bindings of the next arm

If a given way is allowed, all others below it are also allowed.

> _Example_
> ```
> struct Foo {
>     a, b, c: i32
> }
> 
> val := Foo{ a: 0, b: 0, c: 0 }
> 
> match val {
>     .{ a: 0, b: 0, c: 0 } => {
>         
>         // directly go to the next case
>         fallthrough;
>     },
>     .{ a: 0, b: 0, c: 1 } => {
>         // go to the arm with the given label
>         fallthrough :label;
>     },
>     .{ a: 0, b: 0, c: 2 } => {
>         // ...
>     },
>     :label: .{ a: 0, b: 0, c: 3 } => {
>         // fallthrough while passing `4` to `c`, as its the only binding
>         fallthrough 4;
>     },
>     .{ a: 0, b: 1, c } => {
>         // fallthough while passing `5` and `6` to `b` and `c` respectively
>         fallthrough (5, 6);
>     },
>     .{ a: 1, b, c } => {
>         // same as above, as both alternatives have the same order of bindings
>         fallthrough (7, 8);
>     },
>     .{ a: 2, b, c } | .{ a: 3, b, c } => {
>         // pass values as fields with a matching name, as the next arm has alternative with different orders for their bindings
>         fallthrough .{ b: 9, c: 10 };
>     },
>     .{ a: 4, b, c } | .{ a: 5, c, b } => {
>         // ...
>     },
> }
> ```



[`match`]:              ./match-expressions.md
[alternative patterns]: ../patterns/alternative-patterns.md
