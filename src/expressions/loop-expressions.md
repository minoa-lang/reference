# Loop expressions
```
<loop-expressions> := <labeled-block-expr>
                    | <inf-loop-expr>
                    | <while-expr>
                    | <do-while-expr>
                    | <for-expr>
```

A loop expression is a collections of expressions which can repeat an associated block of code multiple times.

There is support for 5 kinds of loop expressions
- a labelled block: this is a _pseudo-loop_, at it only runs once, but has support for an early exit like other loops
- an infinite `loop` expressions: which will run until exited from
- a `while` loop: which will run until its predicate result in `false`
- a `do-while` loop: similar to a `while` loop, but guaranteed to run at least once
- a `for` loop: which will run for each iterable value provided to it

All loops support a so-called _exiting expressions_, these are one of the following:
- [`break`]
- [`continue`]: not supported by labelled blocks

If multiple [`break`]s that can leave the loop are present, and they return a value, all these values must have the same type, or at least be compatible in the case of it either being an optional or result type being set with an assignment shorthand.

## Trivial values [↵](#loop-expressions)

Loops may use the term '_trivial value_'.
This refers to either a [unit] or [never], which does not need an explicit value to be provided when using a [`break`].

## Limitiations [↵](#loop-expressions)

Within the condition of a `while` loop, or the iterable of the `for` loop, there are certain limitations cause by how parsing works.
Therefore, neither may contain any of the following:
- [struct expression]
- [block expression], except when it wraps the entire expression.

unless they are located inside of a:
- [parenthesized expression]
- [block expression]
- [struct expression]

## Labels [↵](#loop-expressions)
```
<label> := ':' <ext-name>
```

Labels are used to indicate certain parts of code which can be reached using an exiting statement.

Labels follow the hygiene and shadowing rules of local variables.



[block expression]:         ./block-expressions.md
[`break`]:                  ./break-expressions.md
[struct expression]:        ./constructing-expressions/struct-expressions.md
[`continue`]:               ./continue-expressions.md
[parenthesized expression]: ./paren-expressions.md
[never]:                    ../type-system/types/builtin-types/never-types.md
[unit]:                     ../type-system/types/builtin-types/unit-types.md