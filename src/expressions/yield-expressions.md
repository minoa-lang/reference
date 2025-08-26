# Yield expressions
```
<yield-expr> := `yield` [ <expr> ]
```

A yield expression allows a generator function to return a value, while still allowing it to produce more values after it.

A function can use any number of `yield`s to return values.

There is a different between a `yield` in a regular and an `async` context:
- when in a regular generator function, a yield is required to have a value.
- when in an `async` generator function, the return value is optional. If no value is provided, the `yield` will just suspend the async function.
