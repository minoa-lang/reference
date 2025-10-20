# When items
```
<when-item> := 'when' <expr> <when-body> [ 'else' ( <when-item> | <when-item-body> ) ]
<when-item-body> := '{' <item>* '}'
```

A when items allow for code to only exists when certain compile-time conditions are fulfilled.

A `when` item can be likened to an [compile-time `if` expression], including the fact that it also takes a compile-time expression.
It can also be compared to a set of items marked with the [`cfg` attribute].

Unlike an `if`, a `when` item does not create a scope, but just adds the code contained within it, into the scope the `when` item is located in.

There also exists both a [statement] and [expression] variant of this item.
These will be used depending on the context of where the `when` is located.

> _Example_: Different version of `Foo` on windows platforms
> ```
> when #target_os == .windows {
>     struct Foo {
>         ...
>     }
> } else {
>     struct Foo {
>         ...
>     }
> }
> ```

[`cfg` attribute]:              ../attributes/conditional-compilation.md#cfg-
[statement]:                    ../statements/when-statement.md "Todo: does not exists yet"
[expression]:                   ../expressions/when-expressions.md
[compile-time `if` expression]: ../expressions/if-expressions.md#compile-time- "Todo: Section does not exists yet"
