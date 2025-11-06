# Block expressions
```
<block-expr> := [ <block-~specifier> ] <block>
<block>      := '{' { <stmt> }* [ <expr> ] '}'
<block-specifier> := 'const'
                   | 'async' [ 'gen' ] [ 'move' ]
```

A block expressions is a an expressions which introduces a new anonymous namespace and scope for items and variable declarations.
Meaning that items and varianbles declared within its scope are not accessible outside of this scope.

Additionally, it allows for a more than just expressions to be defined in a location which normally would only permit a single expression.

A block generally exists out of a set of statements, with an optional final expression.
The block will executed each non-item statement in order, and will finally, if provided, execute the final expression and return its value.
If no final value is provided, the block will have the `()` type.

> _Example_
> ```
> a: () = {
>     println("returns no value");
> }
> 
> b: i32 = {
>     println("returns a value of 5");
>     5
> };
> ```

If a block's final expression is [comma expression], the block will return a tuple of those values.

> _Example_
> ```
> a: (i32, i32) {
>     2, 3
> };
> 
> print("0: \{a.0}, 1: \{a.1}");
> ```

> _Note_: For a labelled version of block, see the [labelled blocks] section.

All blocks are value expresions, and will evaluate its final expression is a value expression context.

## Const block [↵](#block-expressions)

A `const` block is a variant of a block which will run any code contained within it at compile time.

They allow for the defining of a constant value without requiring a [const item] to be created, and can thus also be referred to as _inline constants_.

Since they are blocks, they also allow the constant value to be dependent on any constant or inferred parameter provided to a function.

> _Example_
> ```
> fn Foo[T] (val: T) -> usize {
>     const { size_of(T) + 1 }
> }
> ```

They are essentially the same as a type with an associated constant defined at its position.

> _Example_
> ```
> fn Foo[T] (val: T) -> usize {
>     struct Const[T](T) {
>         const CONST: usize = size_of(T) + 1;
>     }
>     Const[T].CONST
> }
> ```

## Async block [↵](#block-expressions)

An `async` block is a version of block which evaluates into a future, similar to an async function.

The final expressions will return the return type of the created future, otherwise, it will be determined by a [`break`], if the block is labelled.

Async blocks produce type implementing the [`Future`] trait, but the type layout of the produced type is not defined.

> _Example_
> ```
> a := async {
>     await foo();
>     await bar();
> };
> ```

They can be seen as an anonymout async closures with no arguments, which are immediatally executed.

> _Example_
> 
> The block above is similar to the following
> ```
> a := async fn{ 
>     await foo();
>     await bar();
> }();
> ```

If the `async` block is marked as `gen`, it is an async generator.

### Capture modes [↵](#async-block-)

Async blocks capture variables from the surrounding environment, similarly to how a [closure] captures variables.

If captures without `move`, the capture mode of each variable will be inferred from the surrounding context.
When declared with `move`, all values will be moved into the block.

### Async context [↵](#async-block-)

Since an `async` block constructs a future, the contents of it run in an _async context_.
Meaning that `async` blocks may contain [`await`] expressions.

### Control flow expressions [↵](#async-block-)

`async` blocks introduce a function boundary, meaning that cetain control flow expression behave differently than they would in a regular block.

Calling [`return`], [`throw`], [`yield`], or using the [catch operator] affect the future being returned, not the enclosing function or other context.
That is:
- [`return`] and [`yield`] expressions within the block will return the result as the output of the future
- [`throw`] expressions will cause the future to return a [result] type with a compatible error
- the [catch operator] will propagate any error to the return type of the future with a compatible error ([result] or [optional] type)

Meanwhile, [`break`], [`continue`], and [`fallthrough`] cannot go to labels outside of the async block.

_Example_
```
fn foo() {
    // A future with return type `i32`
    a := async {
        // Does not return from `foo`
        return 1;
    };
}
```

### Limitations [↵](#async-block-)

There are certain limitations on which expressions are allowed within an async block.
The following expressions are **not** allowed within an `async` block:
- [`return`]
- [`yield`]
- [`throw`]
- [`break`], [`continue`] or [`fallthrough`] to labels outside of the async block
- [catch operator]


[`Future`]:          #async-block- "Todo: Link to docs"
[`await`]:           ./await-expressions.md
[`break`]:           ./break-expressions.md
[comma expressions]: ./comma-expressions.md
[`continue`]:        ./continue-expressions.md
[`fallthrough`]:     ./fallthrough-expressions.md
[labelled blocks]:   ./loop-expressions.md
[`return`]:          ./return-expressions.md
[`throw`]:           ./throw-expressions.md
[`yield`]:           ./yield-expressions.md
[const item]:        ../items/consts.md
[catch operator]:    ../operators/special-operators.md#catch-operator-
[closure]:           ../type-system/types/function-like-types/closure-types.md#capture-modes-
[result]:            ../type-system/types/abstract-types/result-types.md
[optional]:          ../type-system/types/abstract-types/optional-types.md