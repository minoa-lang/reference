# Return expressions
```
<return-expr> := 'return' [ <expr> ]
```

Return expressions moves its argument into the designated output location for the current function call, destroys the current function activation frame, and transfers control to the caller frame.
When the function being called has named returns, the `return` expression is allowed to overwrite the named return values.

If a return is followed by a comma expression, it will return a tuple, which may be named.

### 9.24.1. Return in generator functions [↵](#return-expressions)

In addition to the regular use of a return, it also has a special meaning in a generator function.
Unlike a [`yield` expression](./yield-expressions.md), which allows the async function to generate more values, `return` fully terminates the function.
