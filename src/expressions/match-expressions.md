# Match expressions
```
<match-expr> := [ <label> ':' ] 'match' <scrutinee> '{' { <match-arm> }* [ <final-arm> ] '}'
<match-arm>  := [ <label> ':' ] <pattern> '=>' ( ( <expr> ',' ) | ( <block> [ ',' ] ) )
<final-arm>  := [ <label> ':' ] <pattern> '=>' ( <expr> | <block> [ ',' ] )
```

The `match` expression which branches based on the value of its scrutinee.
The value will be checked to all the provided arms, and will branch to the first arm with a matching pattern.
Within a pattern, it is checked from left to right.

The scrutinee and patterns within the arms must have the same type.

A `match` behaves differently depending on whether the scrutinee is a place or value expression.
- when it is a [place expression], the match does not need to place it in a temporary location, but when bound to a by-value binding, it may be copied or moved from the memory location.
  If possible, it is prefered to match on a place expression, as the resulting bindings depend on the lifetime, rather than being limited to only inside the `match` expression
- when it is a [value expression], the result of it will first be evaluated and put in a temporary memory location.
  If the expression is matched to an arm, any variables bound will be directly assigned to local variables represented by those bindings.

Bindings within the pattern are scope to the current arm.
Their binding mode (i.e. move/copy, or reference) is determnined by the provided pattern.

Multiple patterns may be joined using an [alternative pattern], e.g. `|`.
As defined by the alternative pattern, any bindings need to appear in all alternatives, with the same binding mode, to be able to be usable within the arm's body.

> _Example_
> ```
> x := 0;
> message := match x {
>     0 | 1 => "not many",
>     2..=9 => "a few",
>     _     => "lots"
> };
> 
> assert(message == "a few");
> 
> struct S(i32, i32);
> 
> match S(1, 2) {
>   S(z @ 1, _) | S(_, z @ 2) => assert(z == 1),
>   _ => #panic(),
> }
> ```

# Scruntinee [↵](#match-expressions)
```
<scrutinee> := ? <expr> except <struct-expr> ?
```

The scrutinee has a certain set of limitation which are caused by how parsing works.
Therefore, it may **not** contain any of the following:
- [struct expression]
- [block expression], except when it wraps the entire expression.

unless they are located inside of a:
- [parenthesized expression]
- [block expression]
- [struct expression]

## Fallthrough labels [↵](#match-expressions)

Since `math` expressions allow for a [`fallthrough`] to be used, each arm may start using a label, a fallthrough may can go to.
This label may only be referenced by a `fallthrough` within the arm's expression.

This label can then be used to evaluate another arm whithin the `match`.

However, labels are only allowed if the arm's pattern does not capture any bindings.

> _Example_
> 
> Because of the `fallthrough`s, both the second and last arm will both be executed.
> ```
> step := 1;
> match step {
>     :start: 0 => {
>         println("start processing");
>         fallthrough :middle:
>     },
>     :middle: 1 => {
>         println("in the middle of processing");
>         fallthrough :end:
>     },
>     :end: 2 => {
>         println("end processing");
>         // ...
>     }
> }
> ```



[`fallthrough`]:       ./fallthrough-expressions.md
[place expression]:    ../expressions.md#place-expressions-
[value expression]:    ../expressions.md#value-expressions-
[alternative pattern]: ../patterns/alternative-patterns.md