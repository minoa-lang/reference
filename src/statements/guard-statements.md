# Guard statements
```
<guard-stmt> := 'if' <branch-condition> 'else' <block>
```

A guard statement is similar to an [`if` expression], but it only has an else branch, meaning the body gets executed if the condition evaluates to `false`.

All codepaths within the body of a guard statement must end with a returning expression:
- [`return` expression]
- [`yield` expression]
- [`throw` expression]

This also means that the guard statement cannot return a value, and may therefore not be part of an expression, i.e. this is why this is a statement.

> _Example_
> ``` 
> a := 3;
> 
> if a == 4 else {
>     println("a is not 4");
>     return;
> };
> ```
> will output
> ```
> a is not 4
> ```



[`if` expression]:     ../expressions/if-expressions.md
[`return` expression]: ../expressions/return-expressions.md
[`yield` expression]:  ../expressions/yield-expressions.md
[`throw` expression]:  ../expressions/throw-expressions.md