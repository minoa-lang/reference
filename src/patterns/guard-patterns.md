# Guard patterns
```
<guard-pattern> := [ <pattern> ] 'if' <expr>
```

Guard patterns match any values that match both the subpattern, and have an expression which results in `true`.
The expression provided to the guard patterns is called the _guard expression_ and must have a [boolean] type.
This allows a match to be narrowed down using in a way other than just a pattern match, and may also provide a way of using runtime code within a match expression.

When the subpattern matches, only then is the guard expression evaluated.
When an alternative expression is provided as a subpattern, the subpattern is evaluated from left to right, and for each matching subpattern, the expression is re-evaluated for each match.

The guard expression may refer to any bindings defined within its subpattern.
When evaluating the guard expression, the bindings will use a reference to the bound value.
Only when the guard evaluates to `true` and any outer pattern is matched, will the value be bound using the binding mode defined by the [identifier pattern].
The shared reference also prevents mutation of the scrutinee in any guard pattern.

> _Example_
> ```
> match opt_int {
>     .Some(0)                          => println("Zero!"),
>     .Some(val) if val.is_power_of_2() => println("Power of 2!")
>     .Some(_)                          => println("Other value"),
>     .None                             => println("No value")
> }
> ```

It is also possible to leave out the pattern, in this case, the pattern will only be matched based on the expression.
This is only allowed when the scrutinee is an identifier and the expression uses that identifier.

> _Example_
> ```
> match val {
>     if val.has_foo() => (),
>     if val.has_bar() => (),
>
>     // error: the guard expression must reference the scrutinee `val`
>     // if a == b => (),
> 
>     Struct { flags } => (),
> }
> ```

Any occurance of a `|` within the guard expression will be parsed as part of the guard expression.
If the `|` should represent an [alternative pattern], the guard expression should be in a [grouped pattern].

> _Example_
> ```
> match value {
>     // error: the pattern will be interpreted as `value if ((value & 1) == 0 | 2)`, which will result in an invalid operation
>     // val if (val & 1) == 0 | 2 => (),
> 
>     // will be correctly interpreted as a value matching either the guard pattern, or 2
>     (val if (val & 1) == 0) | 2 => (),
> }
> ```

Guard pattens are refutable, unless both the subpattern is refutable and the expression can be resolved to a compile-time `true` value.



[alternative pattern]: ./alternative-patterns.md
[grouped pattern]:     ./grouped-patterns.md
[identifier pattern]:  ./identifier-patterns.md
[boolean]:             ../type-system/types/builtin-types/boolean-types.md