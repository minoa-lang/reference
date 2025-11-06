# Type check expressions
```
<type-check-expr> := <expr> <is-op> ( <type> | <trait-bound> )
<is-op> := 'is' | '!is'
```

A type check expression can be seen as a special version of an infix operator, with a type as its right-hand operand.
This operator cannot be manually overwritten and my depend on the existence of type info.

A type check can test if its left-hand operand is/has a certain type, or implements a set of traits.
Additionally, a negated version is provided, which is used to check the inverse, i.e. if its left-hand operands is not/does not have a certain type, or is it does not implement a set of traits.
If type info is available, it can also check if a [trait object] is a given type.

> _Note_: If the type check can be performed at compile time, this will essentially become a boolean constant.

> _Note_: Since type check expressions have a higher precedence that expressions and a trait bound may include a `&`, the expression `a is b & c` will be interpreted as `a is (b & c)` and not `(a is b) & c`.

> _Example_
> ```
> fn generic_fn(const T: type) {
>     // check if `T` is an i32, then we can do some additional logic
>     if T is i32 {
>         // ...
>     }
> 
>     // check if `T` implements the `Copy` interface
>     if T is Copy {
> 
>     }
> 
>     // check if `T` implements both `Bar` and `Baz`, but also that `Baz`'s associate type `Result` is an `i32`
>     if T is Bar & Baz(.Result = i32) {
> 
>     }
> }
> 
> a := 42;
> 
> // Check if the variable `a` has type `i32`
> assert(a is i32);
> 
> // Check if the variable `a` does not have type `f32`
> assert(a !is f32);
> 
> // Check if the variable `a` has a type which implements the trait `Clone`
> assert(a is Clone);
> ```

## Type checks and control flow expressions

When a value is checked to be of a specific type within the condition of a control flow expression (i.e. [`if`] and [conditional loops]), the user can use the value as if it is that type within the rest of the condition, and the body of the expression

> _Example_
> ```
> fn generic_foo[T: type](val: T) {
>     if val is i32 {
>         // val is here used as if it had type `i32`
>         _ = 1 + val;
>     }
> 
>     // similarly, this can also be used within the condition
>     if val is i64 && val + 1u64 == 2u64 {
>         // ...
>     }
> }
> ```



[`if`]:              ./if-expressions.md
[conditional loops]: ./loop-expressions.md
[trait object]:      ../type-system/0