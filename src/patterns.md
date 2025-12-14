# Patterns
```
<pattern>             := <pattern-no-top-alt>
                       | <alt-pattern>
<pattern-no-top-alt>  := <pattern-no-range>
                       | <range-pattern>
                       | <grouped-pattern>
                       | <type-check-pattern>
                       | <guard-pattern>
<pattern-allow-ref>   := <lit-pattern>
                       | <iden-pattern>
                       | <wildcard-pattern>
                       | <ref-pattern>
                       | <struct-pattern>
                       | <tuple-struct-pattern>
                       | ? <grouped-pattern> with an inner pattern matching <pattern-allow-ref> ?
                       | <slice-pattern>
                       | <path-pattern>
```

Patterns are used to match values against structured expressions and to, optionally, bind variables to values inside of these structured expressions.

Patterns are allowed in the following locations:
- [pattern variable declarations]
- [function] and [closure] parameters
- [`match` expressions]
- [`let` bindings]
- [`for` loops]
- [list comprehensions]

> _Example_
> ```
> if let Person {
>         first_name: ref first_name,
>         last_name: "Smith",
>         age @ 18..=65,
>         job: .Some(_)
>     } = person
> {
>     println("\{first_name} Smith is \{age} years old and is employed");
> }
> ```

# Destructuring [↵](#patterns)

Patterns can be used to destructure [composite types].
Destructuring breaks up a value into its constituent elements or componenet pieces.

In any pattern where the scrutinee expression has a composite type, the following expressions have a special meaning:
- a [wildcard pattern] (`_`) represents exactly 1 field, whereas
- a [struct rest pattern] or [rest pattern] represents all remaining fields that are not explicitly specified

When destructuring a value, any named (but not numbered or with an extended name) fields, it is allowed to write `fieldname` as a shorthand for `fieldname: fieldname`.

> _Example_
> ```
> match message {
>     .Quit            => println("Quit!"),
>     .Write(text)     => println(text),
>     .Move{ x, y: 0 } => println("Moved horizontally by \{x}"),
>     .Move{ .. }      => println("Other move"),
>     .ChangeColor{ 0: red, 1: green, 2: blue } => {
>         println("Changed color to r: {}, g: {}, b: {}", red, green, blue);
>     },
> }
> ```

# Refutability [↵](#patterns)

Refutability defined whether a type can be guaranteed to be matched against or not.
A _refutable pattern_ cannot be guaranteed to always match, meaning it may fail to match a value given to it.
On the other hand, an _irrifutable pattern_ can be guaranteed to always match against any given value.

> _Example_
> ```
> tup := (1, 2);
> 
> if let (a, 3) = tup { // refutable pattern, i.e. may fail to match
>     println("Matched a refutable pattern");
> } else if let (a, b) = tup { // irrifutable pattern, i.e. will always match
>     println("Will always match")
> }
> ```

# Constant patterns [↵](#patterns)




[closure]:                       ./expressions/closure-expressions.md#closure-parameters-
[`let` bindings]:                ./expressions/if-expressions.md#let-bindings-
[list comprehensions]:           ./expressions/list-comprehension-expressions.md
[`for` loops]:                   ./expressions/loop-expressions/for-expressions.md
[`match` expressions]:           ./expressions/match-expressions.md
[function]:                      ./items/functions.md#parameters-
[wildcard pattern]:              ./patterns/wildcard-patterns.md
[struct rest pattern]:           ./patterns/struct-patterns.md#struct-rest-pattern-
[rest pattern]:                  ./patterns/rest-patterns.md
[pattern variable declarations]: ./statements/variable-declarations.md#pattern-variable-declaration-
[composite types]:               ./type-system/types/composite-types.md