# Underscore expressions
```
<underscore-expr> := '_'
```

Underscore expressions are used to signify a placeholder, and may be used in the following locations:
- as a placeholder in a [destructuring assignment]
- at the left hand side of an assignment expression, causing it to use, but immediatelly discard the value.
- as an inferred argument for [constant parameters]. This requires the value to be inferrable from the surrounding context
- as a placeholder for function [partial application (currying)]

> _Note_: that this is distinct from a wildcard pattern.

> _Example_
> ```
> mut a: i32;
> 
> tup := (1, 2);
> 
> // discards the value of `tup.1` when assigning
> (a, _) = tup;
> 
> 
> // requires the returned value to be used
> @must_use("return value must be used")
> fn foo() -> i32 { 42 }
> 
> // so this assigned the value, but explicitly discards the value
> _ = foo();
> 
> 
> 
> fn bar(const TY: Type) -> TY { ... }
> 
> // makes `bar` infer the const type parameters from its return value assignment
> a: i32 = bar(_);
> ```



[constant parameters]:            ../items/functions.md#parameter-specifiers-
[partial application (currying)]: ../items/functions.md#partial-application-currying-
[destructuring assignment]:       ../operators.md#destructuring-assignment-