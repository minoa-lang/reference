# Result types
```
<result-type> := [ <path-type> | <builtin-type> ] '!' <type>
```

A result type is used to represent either a valid value, or an error.
A valid value is represented as `.Ok(T)`, and an error as `.Err(E)`.

The syntax is similar to a never type, but is distinguished by having an explicit type attached to it.
The value on the right represents the `T` or valid value, and the path on the left, the `E` or error value.
Meaning that `Result(T, E)` is represented as `E!T`.

The reason for the seemingly inversed argument placement is as follows:
- error type are most commonly able to be refered to by a path, or as a primitive type
- puttin the `T` on the right does not require more complex type to be wrapped, i.e. `!^T` instead of `(^T)!`

> _Note_: If a non-path error type is needed, it is recommened to write an explicit `Result(T, E)` type.

The main purpose of a result type is to indicate a fallible operation with explicit errors.

Additionally, [`if` expressions], [`while` loops], and [`for` loops] support syntax to make checking for errors more ergonomic.
The [`?` or `!` operator] and optional chaining (via `OptAccess`) is also supported for optional types.

Errors can easily produces with help of the [`throw` expression].

## Assignment shorthand [↵](#result-types)

To simplify assigning a non-error value to a result type (i.e. when in the `.Ok(T)` state), a useful shorthand is provided, which can automatically wrap a value with a `.Ok(T)` variant on assignmeht.

> _Example_
> ```
> let v: !i32 = .Err(());
> 
> // automatically wraps `2` in `.Ok(2)`
> v = 2;
> ```

This can also be used to infer the type of the value

_Example_
```
// Since no explicit type is provided, the compiler will default to assigning a `Result(_, ())` type to `v`
let v = .Err(());

// We now assign an integer value (defaulted to i32), so we can now infer that the full type of `v` is `Result(i32, ())`
v = 2;
```

## Internal representation [↵](#result-types)

The result type does not directly map to a builtin type, but is instead a special alias to the following core type:
```
enum Result[T, E] {
    Ok(T),
    Err(E),
}
```
Meaning that any `!T` will be converted to `Result(T, _)`, and any `E!T` to `Result(T, E)`.

In addition, since certain error types require only a couple values, this could be stored within unused values in the type containing it.
When this is the case, the result type will use the unused values to represent the `.Err(E)` state.
An example of this would be a result where the error is represented using a `()` type, which results in a similar optimization to [optional types].

Whether a type can use this optimization can be found using the `ResultOptimization` flag within a type's typeinfo.

A value for this can be manually defined using the [`@val_range`] attribute on a type.

More information about support can be found the [documentation]

> _Todo_: Links for type info flag



[documentation]:              #internal-representation- "Todo: link to docs"
[optional types]:             ./optional-types.md
[`if` expressions]:           ../../../expressions/if-expressions.md#optional-shorthand-
[`while` loops]:              ../../../expressions/loop-expressions.md#result-while-
[`for` loops]:                ../../../expressions/loop-expressions.md#result-for-
[`throw` expression]:         ../../../expressions/throw-expressions.md
[`?` or `!` operator]:        ../../../operators/special-operators.md#try-operator- 