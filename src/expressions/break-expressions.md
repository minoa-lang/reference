# Break expressions
```
<break-expr> := 'break' [ <label> ] [ <expr> ]
```

A `break` expressions allows the surrounding control flow construct to be exited.
This is done either in:
- a [loop expression], it terminates the current iteration of the loop and skips any assoicated `else` blocks
- a [`match` expression], it terminates the current arm and surrounding `match` expression

The `break` is only permitted with the (main) body of the expressions mentioned above.

A `break` expression is normally associated with the innermost loop or `match` that encloses the expression.
A label can be provided to allow the `break` to apply to any outer loop or `match`, as long as they are marked with the same label.

## `break` and loop/`match` values

A `break` expression may be provided with an additional value, which will be passed as the evaluated value of the loop/`match` the `break` applies to.
When no explicit value is provided, the `break` will default to returning a `()` value.

_Example_
```
a := :outer: loop {
    // inner
    while cond() {
        // breaks out of the current 'inner' loop
        break;

        // breaks out of thh current 'inner' loop, and makes it evaluated to a value of `1`.
        break 1;

        // directly breaks out of the `outer` loop
        break :outer;

        
        // directly breaks out of the 'outer' loop, and makes it evaluated to a value of `1`.
        break :outer 1;
    }
}
```



[loop expression]:    ./loop-expressions.md
[`match` expression]: ./match-expressions.md