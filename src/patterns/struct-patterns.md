# Struct patterns
```
<struct-pattern>       := <struct-pattern-path> '{' <struct-pattern-elem> { ',' <struct-pattern-elem> } [ ',' <> ] '}'
<struct-pattern-path>  := <path>
                        | '.'
<struct-pattern-elems> := <struct-pattern-elem> { ',' <struct-pattern-elem> }* [ ',' <struct-pattern-etc> ]
                        | <<struct-pattern-etc>>
<struct-pattern-elem>  := { <attribute> }* <ext-name> ':' <pattern>
                        | { <attribute> }* <int-literal> ':' <pattern>
                        | { <attribute> }* <iden-pattern>
```

Struct patterns match any [`struct`], [`union`], or [`enum`] value that also matches the criteria defined in the subpatterns.
This allows for the value to be deconstructed into its members.

The pattern can either be provided with an explicit path, or a `.`, which will infer the type based on the value being matched against.

The fields of a struct pattern may be referred to either:
- via its name, followed by its subpattern
- via its tuple index, followed by its subpattern
- via an [identifier pattern] with the same name as the field

> _Note_: Using a tuple index is only available when matching against a [tuple struct]

A struct pattern is refutable, if either the pattern refers to a variant of an enum with 2 or more variants, or if any of its field's subpatterns are refutable.

> _Example_
> ```
> match point {
>     Point{ x: 1, y: 2 } => (),
>     // order of the fields does not matter
>     Point{ y: 4, x: 3 } => (),
>     // inferred type
>     .{ x: 5, y: 6 } => (),
> }
> 
> match tuple_point {
>     TuplePoint{ 0: 10, 1: 20 } => (),
>     TuplePoint{ 1: 40, 0: 30 } => (),
>     .{ 0: 50, 1: 60 } => (),
> }
> 
> // directly use identifier pattern within the pattern
> match value {
>     Struct { a: 10, b } => (),
>     Struct { a: 10, mut b } => (),
>     Struct { a: 10, ref b } => (),
>     Struct { a: 10, ref mut b } => (),
> 
>     // identify pattern with bound
>     Struct { a: 10, b @ 5 } => (),
> }
> ```

When a struct pattern is used to match on a union, only 1 field may be specified.
For more info, see [pattern matchining on unions].

> _Example_
> ```
> match union_value {
>     .{ int } => (),
>     .{ uint } => (),
>     .{ float } => (),
> 
>     // error: a struct pattern matching a union only allow a single field
>     // .{ int, uint } => (),
> }
> ```

## Struct rest pattern [↵](#struct-patterns)
```
<struct-pattern-etc> := '..'
```

Rest patterns are not allowed directly within a struct pattern, a struct rest pattern is used instead.
A struct rest pattern is a variant of a [rest pattern] that matches any unspecified fields within the struct expression.

Unlike the rest pattern, it does not support binding any of the values.

> _Example_
> ```
> struct Foo {
>     a, b, c: i32,
> }
> 
> match foo {
>     // explicitly ignored fields
>     Foo { a: 1, b: _, c: _ } => println("foo has an `a` value of 1"),
> 
>     // ignores both `b` and `c` with a single subpattern
>     Foo { a: 2, .. } => println("foo has an `a` value of 2"),
> 
>     // Ignores all fields
>     Foo { .. } => println("Any other value"),
> }
> ```

If a rest pattern is not used, all fields must be specified within the pattern.

> _Example_
> ```
> match value {
>     Struct{ a: 1, b: 2, c: 3 } => (),
> 
>     // error: fields `b` and `c` are not specified within the struct pattern
>     // Struct{ a: 1 } => (),
> 
>     // This can be resolved either by explicitly mentioning all fields
>     Struct { a: 1, b: _, c: _ } => (),
> 
>     // or by using the struct rest pattern
>     Struct { a: 1, ... } => (),
> }
> ```



[identifier pattern]:           ./identifier-patterns.md
[rest pattern]:                 ./rest-patterns.md
[`enum`]:                       ../type-system/types/composite-types/enum-types.md
[`struct`]:                     ../type-system/types/composite-types/struct-types.md
[tuple struct]:                 ../type-system/types/composite-types/tuple-struct-types.md
[`union`]:                      ../type-system/types/composite-types/union-types.md
[pattern matchining on unions]: ../type-system/types/composite-types/union-types.md#pattern-matching-on-unions-