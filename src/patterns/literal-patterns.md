# Literal patterns
```
<lit-pattern> := [ '-' ] <literal-expr>
```

A literal patten matches the exact value of the provided literal value.
Since literals cannot contain a `-` sign, literal patterns add support for this, as a negative value is a still a literal value which can be matched against.

Literal operators are supported when they can be evaluated at compile time.

Literal patterns are always refutable.

> _Example_
> ```
> match i {
>     -1    => println("Negative one"),
>     0     => println("Zero!"),
>     2 | 4 => println("A positive even value below 5"),
>     10i32 => println("10 with a literal operator"),
>     _     => println("Any other value")
> }
> ```