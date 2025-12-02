# Comma expressions
```
<comma-expr> := <comma-elem> { ',' <comma-elem> }*
<comma-elem> := [ <ext-name> ':' ] <expr>
```

Comma expression combine multiple expressions into a single one, where each sub-expression is separated by a comma.

This is generally quite a nice expression and has limited usecases, which are the following:
- value of a [`return` expression]
- the right-hand operand of an [index expression], when using [labelled indexing]
- the right-hand operand of a [`consume` operator]
- either side of an [assignment operator]

In addition, when used in a [`return` expression], each element may contain an optional name, this will then be used as the name of a 

> _Example_
> ```
> a, b = (1, 2);
> 
> c, d = a, b;
> 
> return 1, name: 3;
> ```


[index expression]:    ./index-expressions.md#multi-value-indexing-
[labelled indexing]:   ./index-expressions.md#labelled-indexing-
[`return` expression]: ./return-expressions.md
[`consume` operator]:  ../operators.md#consume-
[assignment operator]: ../operators.md#assginment-operators-