# Try expressions
```
<try-expr> := 'try' [ '?' | '!' ] <expr>
```

The try expression allows the code to handle both optional and result values in 3 ways:
- `try`: the propagating try, if the value is a `None` or `Err()`, the code will [throw the error as defined above]
- `try!`: the unwrapping try, if the value is a `None` or `Err()`, the code will panic

These operators have the following operator traits associated with them:
- `try`: `Try` 
- `try!`: `TryUnwrap`

When a try expression is followed by a block, it's a so-called try-block.
In a try-block, the try applied to the block will be applied on every call that supports the correspondin try, unless they are explicitly handled.

Each try has their corresponding post-fix operator version which can be nested within other expressions.
These operators are the [try operators]



[throw the error as defined above]: ./throw-expressions.md
[try operators]:                    ../operators/special-operators.md#try-operator-