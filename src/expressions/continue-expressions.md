# Continue expressions
```
<continue-expr> := 'continue' [ <label> ]
```

When `continue` is encountered, the current iteration of the associated loop body is immediatally terminated, returning control to the loop head.
These correspond to the following for given loops:
- `loop`: the head is the the start of the loop body
- `while` loop: the head is the increment (if it exists), or the conditional expression controllering the loop (which also always follows the increment).
- `do while` loop: the head is the conditional expression controlling the loop
- `for` loop: the head is the call expression controlling the loop
In addition, when used withing a `match`, it may also re-evaluate the `match` expression, an example of this would be a state machine.

Like a `break`, `continue` is normally associated with the innermost enclosing loop, but `continue 'label` may be used to specify the loop affected.
A `continue` expression is only permitted in the body of a loop.

> _Note_: The benefit of using `continue` in a `match` over using a loop, is that it allows a jump to be inlined at the tail of a branch, which may allow for better branch prediction.
