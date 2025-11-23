# Do-while expression
```
<do-while-expr> := [ <label> ':' ] 'do' <block> 'while' <condition> <do-while-tail>
<do-while-tail> := ';'
                 | 'else' <block>
```

A `do`-`while` loop is similar to a [`while`] loop, except that it is guaranteed to run at least once.
This happends because the conditon is only checked after the loop is run, not before it like in a `while` loop.

If the loop expression does not explicitly return a value, it will have a `()` ([unit]) type.
Otherwise, if the loop contains a [`break`], the expressions will have the type of the value returned by the break.

> _Example_
> ```
> do {
>     // code is always guaranteed to run once, regardless of the condition below
> } while cond();
> ```

## `do`-`while`-`else` [↵](#do-while-expression)

A `do`-`while` loop may contain an `else` block at the end of its main block.

The `else` block will only execute based in the following 2 conditions:
- if a `do`-`while` contains a `break` with a non-trivial value, an `else` block is required to be added if not all path eventually result in a `break`.
  This is required since the loop needs to return a value of the same type in all its possible codepaths, which includes the path where no explicit `break` is encountered.
  In this case, the `else` block must return a compatible type to that of the `break`s.
- if a `do`-`while` contains no `break`, or one that only returns a trivial value, the `else` block will only be executed if no subsequent iteration is entered after the initial iteration.
  Meaning that this will only happend when the initial predicate has a value of `false`, and not when the loop is exited at any later stage.


> _Example_
> ```
> // do-while will either return a value using the break statement, or will return a value from the `else` block
> do {
>     if a == b {
>         break 1;
>     }
> } while cond() else {
>     2
> }
> 
> 
> // if after the first iteration, `cond2()` returns a false value, the else block will execute, otherwise it will run until `cond2()` on any other iteration and will skip the else block
> while cond2() {
>     // ...
> } else {
>     // code will run if `cond2()` returns a `false` as it's first result
>     // ...
> }
> ```


[`while`]: ./while-expressions.md