# For expressions
```
<for-expr> := [ <label> ':' ] 'for' <pattern> 'in' <expr> <block> [ 'else' <block> ]
            | <res-for-expr>
```
A `for` expression repeats the execution of its body until the provided iterable is exhausted.
The iterable is provided by a type implementing the `Iterator` trait, which may also be implictly be derived from the type, if it implements `IntoIter`.

The resulting value can be matched using the provided pattern, which must be an irrefutable pattern.

> _Example_
> ```
> for i in 0..10 {
>     println("\{i}");
> }
> ```

## `for`-`else`

A `for` loop may contain an `else` block at the end of its main block.

The `else` block will only execute based in the following 2 conditions:
- if a `for` contains a `break` with a non-trivial value, an `else` block is required to be added if not all path eventually result in a `break`.
  This is required since the loop needs to return a value of the same type in all its possible codepaths, which includes the path where no explicit `break` is encountered.
  In this case, the `else` block must return a compatible type to that of the `break`s.
- if a `for` contains no `break`, or one that only returns a trivial value, the `else` block will only be executed if the iterable does not provide any values.
  Meaning that this will only happend when the iterable does not provide any value, and not when the loop is exited at any later stage.

> _Example_
> ```
> enum Foo { A(i32), B(i32), C(i32) }
> 
> fn get_foo() -> Iterable[Foo] { ... }
> 
> for .A(val) in get_foo() {
>     // ...
> } else {
>     // will run if it fails to match
> }
> ```

## Result `for` [↵](#for-expressions)
```
<res-for-expr> := [ <label> ':' ] 'for' <pattern> 'in' [ '?' ] <expr>  <block> 'else' '(' <name> ')' <block>
```

A result `while` is similar to a [result `if`],this allows for a value to be either map to the main or else branch of the `while` expression.
This can be done in 2 ways:
- if the `in` keyword is *not* followed by a `?` and the provided pattern is refutable, the main branch will be entered when the pattern matches.
  Otherwise the full unmatched value will be passed to the `else` branch.
- if the `in` keyword is followed by a `?`, it allows for a value with 2 possible variants to be mapped to the branches.
  The provided pattern must be infallible.
  The main branch will be executed if the shorthand results in a match, otherwise it will be passed to the `else` branch.

  This is the `for` loop's equivalent to the optional shorthand.

  Additionally, it is possible to explicitly pass a name to bind the resulting value of a `true` branch to.
  If not passed, only named values will be bound.
  
  By default, this is only support for values of a [result type].
  Additional types may provide this functionality by adding the [`@bind_shorthand` attribute] to the type it will apply to, requiring the optional second argument to be passed.

  > _Todo_: `@bind_shorthand` is passed 2 patterns, which it will be matched to, with each having a single binding, e.g. for `@bind_shorthand(.Ok(val), .Err(val))`, where `val` is replace by the compiler

The `else` branch must always define a name to bind a value to.

If the `for` loop reaches the last iterable value, the loop will just exit and the `else` branch will be skipped, unlike a [`while`] loop.

> _Example_
> 
> The pattern matching variant
> ```
> enum Foo { A(i32), B(i32), C(i32) }
> 
> fn get_foo() -> Iterable[Foo] { ... }
> 
> for .A(val) in get_foo() {
> 
> } else (foo) {
>     // `foo` can be used, even if in a regular `if` expression, it would otherwise have been discarded
>     // `foo` has the value returned by `get_foo()`, not a pattern matched one
> }
> ```
> 
> The shorthand version
> ```
> fn get_foo() -> Iterable[i32!bool] { ... }
> 
> for foo in? get_foo() {
>     // valid case code
> } else (err_code) {
>     // error case code
> }
> ```

[`while`]:     ./while-expressions.md
[result `if`]: ../if-expressions.md#result-if-expressions-