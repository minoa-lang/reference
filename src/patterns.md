# Patterns
```
<pattern> := <pattern-no-top-alt> ( | <pattern-no-top-alt> )*
<pattern-no-top-alt> := <pattern-no-range>
                      | <range-pattern>
<pattern-no-range> := <lit-pattern>
                    | <identifier-pattern>
                    | <wildcard-pattern>
                    | <reference-pattern>
                    | <struct-pattern>
                    | <tuple-struct-pattern>


```

Patterns are both used to match values, but also to optionally bind them.
They are used in the following:
- [variable declarations] using `let`
- [Function] and [closure] parameters
- [`match` expressions]
- [conditional `let` bindings]
- [`for` loops]

Patterns can be used to destructure types like struct, enums, and tuples.
Destructuring breaks up a value in its constituent elements.

Patterns can be said to be refutable if there is a possibility for it to not be matched, otherwise if they will always be matched, they are said to be irrifutable.



[closure]:                    ./expressions/closure-expressions.md
[`match` expressions]:        ./expressions/match-expressions.md
[conditional `let` bindings]: ./expressions/if-expressions.md#let-bindings-
[`for` loops]:                ./expressions/loop-expressions.md#for-expression-
[Function]:                   ./items/functions.md#parameters-
[variable declarations]:      ./statements/variable-declarations.md