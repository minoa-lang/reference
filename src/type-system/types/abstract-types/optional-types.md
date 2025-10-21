# Optional types
```
<optional-type> := '?' <type>
```
An optional type is used to represents the presence or absence of a value.
The presence of a value is represented using the `.Some(T)` variant, while the absence is represented using a `.None` state.

Some usecases of an optional type would be:
- an initial value, which can later be set to an actual value
- return values for function that are not defined over an entire input
- return values for function that instead of a simple error, return `null` to indicate something went wrong, i.e. when only 1 possible error can happen
- optional fields within a type
- fields that can be 'loaned' from or 'taken out' of a type
- [nullable/optional pointers], including when containing [`allowzero` pointers]

They are commonly paired with pattern matching to check for the presence of a value and take action based on it.

Additionally, [`if` expressions], [`while` loops], and [`for` loops] support syntax to make checking for `null` value more ergonomic.
The [`?` or `!` operator] and optional chaining (via `OptAccess`) is also supported for optional types.

## `null` [↵](#optional-types)
```
<null-expr> := 'null'
```

The `null` value is special, as it does not represent an actual `null` value, instead it is a special alias for the `.None` value.
The benefit about using `null` over `.None` is the following
- `null` is not a path, so:
  - does not need a preceeding `Option.` or `.`
  - does not conflict with any locally visible values with a `.None` variant
- it is a keyword, which makes it much easier to spot, even with a very basic syntax hightlighting
- it plays nicer together with the assignment shorthand, with `= null` being the counterpart of `= value`

> _Note_: A `null` is also known as `nil` in some other langauges

## Assignment shorthand [↵](#optional-types)

Since an optional type can only contain a value when it is in the `.Some(T)` state, it allows for a useful shorthand to be provided, which can automatically wrap a value with a `.Some(T)` variant on assignment.

> _Example_
> ```
> let v: ?i32 = None;
> 
> // automatically wraps `2` in `.Some(2)`
> v = 2;
> ```

This can also be used to infer the type of the value.

> _Example_
> ```
> // The compiler can already infer the type of be of the type `Option[_]`
> let v = null;
> 
> // We now assign an integer value (defaulted to i32), so we can now infer that the full type of `v` is `Option[i32]`
> v = 2
> ```

## Internal representation [↵](#optional-types)

The option type does not directly map to any builtin type, but is instead a special alias to the following core type:
```
enum Option[T] {
    Some(T),
    None
}
```

Meaning that any `?T` will be converted to `Option[T]`.

In addition, since only a single value is needed to represent a `.None` value, this can quite often allow the optional value to be stored within an unused value in the type containing it.
When this is the case, the optional type will use one of the unused values to represent the `.None` state.
This is most commonly done with type which do not allow a zero value.

An example of a type which support this, would be a pointer type, resulting in a [nullable/optional pointer].
Since a pointer cannot represent an address of `0x0000000000000000`, it can be used as the optional's `None` value.

Whether a type can use this optimization can be found using the `NullOptimization` flag within the type's typeinfo.

A value for this can be manually defined using the [`@val_range`] attribute on a type.

More information about support can be found the [documentation]

> _Todo_: Links for type info flag


[documentation]:              #internal-representation- "Todo: link to docs"
[nullable/optional pointer]:  ../pointer-like-types/pointer-types.md#optional-pointers-
[nullable/optional pointers]: ../pointer-like-types/pointer-types.md#optional-pointers-
[`allowzero` pointers]:       ../pointer-like-types/pointer-types.md#allowzero-
[`@val_range`]:               ../../../attributes/code-generation.md#val_range-
[`if` expressions]:           ../../../expressions/if-expressions.md#optional-shorthand-
[`while` loops]:              ../../../expressions/loop-expressions.md#result-while-
[`for` loops]:                ../../../expressions/loop-expressions.md#result-for-
[`?` or `!` operator]:        ../../../operators/special-operators.md#try-operator- 