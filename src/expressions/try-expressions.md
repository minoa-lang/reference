# Try expressions
```
<try-expr> := 'try' [ '?' | '!' ] <expr> { <catch-expr> }*
```

The `try` expressions has 3 different variants:
- `try`: will check the returned value, if a value is an erroneous value, it will re-[`throw`] the error
- `try?`: the propagating try, if a value is erroneous, it will be the evualuated value of the expression
- `try!`: the unwrapping try, if a value is an erroneous value, it will panic

The expressions work differently depending on whether they are applied to a simple or chained expression.

When applied to a simple expressions, they correspond directly to the following operators/expression:

try expression | corresponding expression | operation
---------------|--------------------------|---------------------------
`try expr`     | `expr?`                  | [`?` or try operator]
`try? expr`    | `expr`                   | propagates the value of the inner expression
`try! expr`    | `expr!`                  | [`!` or unwrap operator]

However, when applied to a chained expression, its will insert additional check on any expression which contains a type that supports erroneous values

try expression | operation on every sub-expression returning an erroneous type
---------------|-------------------------------------------------------------------
`try expr`     | applies the [`?` or try operator]
`try? expr`    | applies optional chaining, coercing to the common erroneous value
`try! expr`    | applies the [`!` or unwrap operator]

These are done, unless the expression using them indicates the values expect the erroneous value supporting type

> _Example_
> ```
> struct Foo {
>     val: ?i32
> }
> 
> struct Bar {
>     foo: ?Foo
> }
> 
> 
> bar := ...;
> 
> 
> try bar.foo.val;
> // equivalent to
> ((bar?).foo?).val
> 
> try? bar.foo.val;
> // equivalent to
> bar?.foo?.val
> 
> try! bar.foo.val;
> // equivalent to
> bar!.foo!.val
> ```
> Mixed erroneous value supporting types with `try?` results in an error
> ```
> struct Baz {
>     foo: i32!Foo,
> }
> 
> // error, cannot return a common erroneous value supporting type that result in a union of `?_` and `i32!_`
> try? bar.foo.val;
> ```

> _Tooling_: It might be helpful for tooling to show the user where the checks are inserted.
>            e.g. when using `try?`, showing `?.` where a null-propagating access would be used instead of a simple `.`, but with the `?` highlighted as tool added

## Interaction with `catch` expressions [↵](#try-expressions)

The `try` expression is also designed to work in tandem with the [`catch` expression].
When using `try?` or `try!`, the special behavior defined in this section is not supported.

When a `try` is followed by a `catch` expression, instead of returining from the current function, it will instead pass the value of the `catch` expression.

Additionally, when the `try` sub-expression were to return a multiple different error types, either:
- a single catch can be provided, in this case, the `try` will convert the erroneous value to a type which support all possible erroneous values
- multiple catches can be provided, each with the specific type of one or the erroneous values.

> _Example_
> ```
> struct Foo {
>     val: i32!bool;
> }
> 
> foo := Foo{ val: null };
> 
> try foo.val catch(err_code) {
>     // log and return error
>     println("Accessed foo.val, got error \{err_code}");
>     throw err_code;
> };
> ```
> 
> When multiple error types are possible, as defined below
> ```
> struct Foo {
>     val: ValError!bool
> }
> 
> foo: FooError!Foo := ...;
> ```
> 
> 
> it can be handled in the following ways:
> - when a common error type is available
>   ```
>   // `err` has the common erroneous value type
>   try foo.val catch(err) {
>       println("common error enountered");
>       null
>   };
>   ```
> - when no common error type is available
>   ```
>   try foo.val catch(err: FooError) {
>       println("FooError encountered");
>       null
>   } catch(err: ValError) {
>       println("ValError encountered");
>       null
>   }
>   ```
> 
> In addition, a `try?` is equivalent to the following:
> ```
> try expr catch(err) err;
> ```
> where the value of `err` has the common erroneous value supporting type.


[`throw`]:                 ./throw-expressions.md
[`catch` expression]:      ./catch-expressions.md
[`?` or try operator]:     ../operators/special-operators.md#try-operator-
[`!` or unwrap operator]:  ../operators/special-operators.md#unwrapping-try-
[optional type]:           ../type-system/types/abstract-types/optional-types.md