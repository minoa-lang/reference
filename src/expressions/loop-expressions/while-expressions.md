# While expression
```
<while-loop> := [ <label> ':' ] 'while' <condition> [ ';' <expr> ] <block> [ 'else' <block> ]
```

A `while` loop repeats the execution of its body as long as the predicate results in a `true` value.
This condition will be checked before each iteration of the loop, meaning that if initial value of the predicate is `false`, the body will never execute.

If the loop expression does not explicitly return a value, it will have a `()` ([unit]) type.
Otherwise, if the loop contains a [`break`], the expressions will have the type of the value returned by the break.

> _Example_
> ```
> // execute the loop's body until `value` is false
> while value {
>     // ...
> }
> ```

## Incrementer [↵](#while-expression)

A while loop may also support an _incrementer_, this is an expressions which runs after each iteration of a loop, and is generally means to increment a value that is used to control the `while` loop's execution.
This feature can be used to emulate a `C`-style `for` loop.

> _Example_
> 
> Emulating a C-style for loop using a while with an incrementer
> ```
> mut index := 0;
> while index < 10; index += 1 {
>     // ..
> }
> ```

## `while`-`else` [↵](#while-expression)

A `while` loop may contain an `else` branch at the end of its main branch.

The `else` branch will only execute based in the following 2 conditions:
- if a `while` contains a `break` with a non-trivial value, an `else` branch is required to be added if not all path eventually result in a `break`.
  This is required since the loop needs to return a value of the same type in all its possible codepaths, which includes the path where no explicit `break` is encountered.
  In this case, the `else` branch must return a compatible type to that of the `break`s.
- if a `while` contains no `break`, or one that only returns a trivial value, the `else` branch will only be executed if the first iteration is not entered.
  Meaning that this will only happend when the initial predicate has a value of `false`, and not when the loop is exited at any later stage.

> _Example_
> ```
> // while will either return a value using the break statement, or will return a value from the `else` block
> while cond() {
>     if a == b {
>         break 1;
>     }
> } else {
>     2
> }
> 
> // if the first call to `cond2()` returns a false value, the else block will execute, otherwise it will run until `cond2()` on any other iteration and will skip the else block
> while cond2() {
>     // ...
> } else {
>     // code will run if `cond2()` returns a `false` as it's first result
>     // ...
> }
> ```

## Result `while` [↵](#while-expression)
```
<res-while-expr> := 'while' <res-if-cond> <block> 'else' '(' <name> ')' <block>
```

A result `while` is similar to a [result `if`],this allows for a value to be either map to the main or else branch of the `while` expression.
This can be done in 2 ways:
- if a `let`-binding is provided and the pattern is refutable, the main branch will be entered when the pattern matches.
  Otherwise the full unmatched value will be passed to the `else` branch.
- if a optional shorthand is used, it allows for a value with 2 possible variants to be mapped to the branches.
  The main branch will be executed if the shorthand results in a match, otherwise it will be passed to the `else` branch.

  This can be compared the optional shorthand specified above, but will also bind the 'error' value to the `else` branch.

  Additionally, it is possible to explicitly pass a name to bind the resulting value of a `true` branch to.
  If not passed, only named values will be bound.
  
  By default, this is only support for values of a [result type].
  Additional types may provide this functionality by adding the [`@bind_shorthand` attribute] to the type it will apply to, requiring the optional second argument to be passed.

  > _Todo_: `@bind_shorthand` is passed 2 patterns, which it will be matched to, with each having a single binding, e.g. for `@bind_shorthand(.Ok(val), .Err(val))`, where `val` is replace by the compiler

The `else` branch must always define a name to bind a value to.

The `else` branch will always run at the end of the loop, therefore the main purpose of this loop is be able to access the 'error' value of the value which would otherwise be discarded.

> _Example_
> ```
> while val = gen_val()? {
>     // do things with a valid value
> } else (err) {
>     // handle invalid value without it being discarded
> }
> ```


[`break`]:                     ../break-expressions.md
[result `if`]:                 ../if-expressions.md#result-if-expressions-
[unit]:                        ../../type-system/types/builtin-types/unit-types.md

[`@bind_shorthand` attribute]: #result-while- "Todo: attribute does not exist yet"