# When statements
```
<when-stmt> := 'when' <expr> <when-body> [ 'else' ( <when-stmt> | <when-stmt-body> ) ]
<when-stmt-body> := '{' <stmt>* '}'
```

A `when` statement is the statement version of a [`when` item], which can be located within a location which allows statements.

More informtation can be found in it's section.

> _Example_
> ```
> when #target_os == .windows {
>     a := 1;
>     println("we're on windows");
> } else {
>     a := 2;
>     println("This isn't windows");
> }
> ```



[`when` item]: ../items/when-items.md