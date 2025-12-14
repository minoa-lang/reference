# Alternative patterns
```
<alt-pattern> := <pattern-no-top-alt> { | <pattern-no-top-alt> }*
```

Alternative patterns match any value that matches any of its subpatterns.
This allows for a value to be matches against multiple patterns within a single match.

> _Example_
> ```
> match int {
>     1 | 2 | 3 => println("value is 1, 2 or 3"),
>     _         => (),
> }
> ```

It is possible for [identifier patterns] to be located within one of the alternative subpatterns.
This requires that all alternatives have a binding with the same binding mode and type.

One exception on this rule, is if a nested binding is located in only one alternative, but it is only used within a [guard pattern].

> _Example_
> ```
> match value {
>     // both alternatives have a binding called `int`, so it can be used
>     FooA { int } | FooB { int } => println("\{int}"),
> 
>     // error: `int` is not a common binding between alternatives
>     FooA { int } | FooC { uint } => println("\{int}"),
> 
>     // error: both bindings have non-matching types
>     // FooA { int: val } | FooD { uint: val } => println("\{val}"),
> 
>     // error: both bindings have non-matching binding modes
>     // FooA { int } | FooB { ref int } => println("\{int}"),
> }
> ```

> _Note_: For the purpose for parsing, an alternative pattern has the lowest precedence, meaning any sub-expression will be parsed fully, before parsing any possible alternative pattern.
>         If the alternative pattern should have a higher precedence, it must be explicitly placed with a [grouped pattern].
>
> For example `x @ A(..) | B(..)` will be parsed as `(x @ .A(..)) | (.B(..))`, meaning that a compilation error will occur because `x` is only bound in one of the alternatives.
> To get around this, an explict group expression should be used, i.e. `x @ (.A(..) | .B(..))`

# Semantics

Given an alternative pattern `p | q`, both `p` and `q` need to follow the following set of rules.

1. When assuting that type conversions are not allowed, and they are unified in the same manner, the pattern is ill-formed when:
    - the type inferred for `p` does not unify with the type inferred for `q`, or
    - the same set of bindings are not introduces in `p` or `q`, or
    - the type of any 2 bindings with the same name in both `p` and `q` do not unity with eachother in respect of them having:
      - different binding modes, or
      - different type
2. When checking for an expression matching `match e_s { a_1 => e_1, ..., a_n => e_n }`, for each arm `a_i` which contains a pattern in the form `p_i | q_i`,
   is considered ill-formed when, at any depth `d`, there is a fragment of `e_s` at depth `d`, where the type of the expression does not unify with type of `p_i | q_i`.

   > _Example_
   > ```
   > let val = Foo.Bar(4);
   > 
   > match val {
   >     // for the pattern `0 | 4` and value `Foo.Bar(value)`, the value of `value` must unify with the 
   >     .Bar(0 | 4) => (),
   >     _ => (),
   > }
   > ```

3. When it comes to deciding the exhaustiveness of a pattern, it can be transformed to a top-level only alternative pattern.
   For any pattern constructed in the form of `c(x, ..rest)`, where `c` represents any pattern constructed using `x` and, optionally, any other patterns.
   It is possible to apply the distributative law to it, so that `c(p | q, ..rest)` can cover `c(p, ..rest) | c(q, ..rest)`.
   This process can then be applied recursively until there is no more nested alternative pattern present within the expression.

   > _Example_
   > ```
   > match value {
   >      .Bar(0 | 4) => (),
   >      _ => (),
   > }
   > ```
   > can be converted to the following for exhaustiveness checking
   > ```
   > match value {
   >      .Bar(0) | .Bar(4) => (),
   >      _ => (),
   > }
   > ```



[identifier patterns]: ./identifier-patterns.md
[grouped pattern]:     ./grouped-patterns.md
[guard pattern]:       ./guard-patterns.md