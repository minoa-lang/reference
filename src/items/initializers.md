# Initializers
```
<init-item> := <fn-qualifiers> 'init' [ '?' | ('!' [ <path-type> | <builtin-type> ] ) ] 'fn' [deduced-params] '(' <fn-params> ')' [ <where-clause> ] { <contract> }* <block>
```

An initializer, also known as an init function, is a special function that allows the for the construction of a type.
Although they are functions, they act differently than regular functions, and are called with a different syntax.

As they are function, they are also allowed to take in the same kinds of deduced and regular parameters.

An initializer has an implicit return, which depends on its kind, these are:

init kind   | return type
------------|-------------
`init`      | `Self`
`init?`     | `?Self`
`init!`     | `!Self`
`init!Type` | `Type!Self`

Each initializer will get access to a possibly unitialized `&mut self`, meaning that within the initializer, there is no guarantee that a value will be assigned before use.
Therefore, the compiler will return an error when it knows a value will be accessed before being initialized.

The initializer must also ensure that all values will be assigned, either explicitly, or via a field's default value.
Not assigning all field will result in an error.

An intializer is called using a [call expression], where the value for the function is the type to initialize.

Since we know the the value we will assign to is initialized, this also gives us the additional capability to be able to assign immutable fields directly.

> _Note_: The reason why an intializer takes in an uninitialized `&mut self`, is to allow the use of initializers with the [in-place operator].
>         This also means that

> _Example_
> ```
> struct Foo {
>     a: i32,
>     b: i32
> 
>     init fn(val: i32) {
>         // We can access the implicit `self` and assign to its immutable field directly
>         self.a = val;
>         self.b = val + 1;
>     }
> }
> ```

It is also possible to return a value directly from the function, if a valid value is return, this will then be in-place assigned to the `self` implicit parameter.

> _Example_
> ```
> struct Foo {
>     a: i32,
>     b: i32,
> 
>     init fn() {
>         Self {
>             a: 1,
>             b: 2,
>         }
>     }
> }
> ```
> is equivalent to the following
> ```
> struct Foo {
>     a: i32,
>     b: i32,
> 
>     init fn() {
>         *self = Self {
>             a: 1,
>             b: 2,
>         }
>     }
> }
> ```


This also means that any function which could rely on a field, cannot be called before these fields are explicitly initialized.

> _Example_
> ```
> struct Foo {
>     a: i32,
>     b: i32,
> 
>     init fn(val: i32) -> {
>         // error: calling this here, will result in the use of an uninitialized value
>         // self.b = self.calculate_b(val)
> 
>         self.a = val;
> 
>         // we can now call this function, as `a` is guaranteed to have a valid, even in the case `calculate_b` does not use the field
>         self.b = self.calculate_b(val);
>     }
> 
>     // Requires access to `a`
>     fn(&self) calculate_b(val: i32) {
>         if val < 3 {
>             self.a + 1
>         } else {
>             val
>         }
>     }
> }
> ```

Multiple initializers are allowed, which must be distinguished by a combination of regular parameters.

> _Example_
> 
> Below is an example of possible initializers, their pseudo-identifiers they have, and how they are distinguished
> 
> ```
> struct Foo {
>     // identifier: __init(val:)
>     init fn(val: i32) {}
> 
>     // valid, has different parameters
>     // identifier: __init()
>     init fn() {}
> 
>     // error: while it is a different kind, it is cannot be disambiguated from the kind
>     // identifier: __init(val:)
>     init? fn(val: i32) {}
> 
>     // error: same as above
>     // identifiers: __init(val:)
>     init! fn(val: i32) {}
> 
>     // invalid, initializer has already been defined above
>     // identifier: __init(val:)
>     // init!bool fn(val: i32) {}
> }
> ```

## Implicit initializers [↵](#initializers)

Even when no explicit initializer is defined, a [composite type] will have a default initializer.

This default initializer comes in 2 forms:
- as a initializer taking a value for each field in case of a tuple-like type
- as a [struct expression] for non-tuple-like types

As implied, if any explicit initializers are provided, these initializers cannot be used.
Specifically, they cannot be used from outside of the type's implementations, the type may still use these internally.
Here, the default initializer of tuple-like type will stop existing, if an initializer with an identical signature is provided.

## Generics & initializers [↵](#initializers)

When a type has generic parameters, it would be expected that an initializer can only have its arguments passed after explicitly providing the generic arguments, e.g. `struct Foo[T](T)` would be constructed as `Foo[i32](2)`.
But this is not necessarily the case, as long as these values can be derived from the initializer, the initializer may be directly called on the type, .e.g. `Foo(2)`.

Generic arguments can be left out of the type when:
- the values can be derived from the initializer (all generic parameters must be provided in the initializer)
- the values can be derived from the bound on the initializer

To allow for this, an initializer must take in values in one of the following ways:
- as part of a type, allows implicit inferrence
- as a parameter of the function, with a matching name and type

If a parameter is missing, the type must first be provided all generic parameters before being able to be called.
Any of these parameters which can be derived from the initializer may be inferred by providing a `_`.

> _Example_
> ```
> struct Foo[T, N: usize] {
>     arr: [N]T
> 
>     // `T` and `N` for the type can be derived from the parameter (0)
>     init fn(arr0: [N]T) {
>         self.arr = arr;
>     }
> 
>     // `T` cannot be derived directly, so both generic arguments must be explicitly passed to the type when calling (1)
>     init fn[U](arr1: [N]U) {
>         ...
>     }
> 
>     // `M` can be derived from the parameter, and `T` can be derived from the bound applied on `U` (2)
>     init fn[U](arr2: [N]U) where U: HasAssocType(.T = T) {
> 
>     }
> }
> 
> // when calling (0), the generic parameter do not have to be provided
> f := Foo(arr0: [0, 1]);
> 
> // when calling (1), the generic paramters must be provided before the initializer can be called, but `N` can be left as `_`, as it can be inferred
> f := Foo[i32, _](arr1: [0, 1]);
> 
> // when calling (2), `T` is derived from the arguments, so the generic paramters do not have to be provided
> f := Foo(arr2: [0, 1]);
> ```

## Default initialization [↵](#initializers)

Default initialization allows some fields to be skipped during initialization.
This is done to help avoid reptitive code for value which will always have the same default values.

Defalut values can be provided in ways:
- if a field has an initial value declared, it will be used
- if the type implements [`Default`], the value for that field defined within `Default.default()` will be used.
- if the type is an optional type, `null` will be assigned to it

> _Note_: No confusion between a field initial value or value returned by `Default.default()` occurs, as by definition, `Default.default()` must return the field initial value for those fields.

## Initializer delegation [↵](#initializers)

Initializers are allowed to delegate part of their initialization to another initializer, this is called initializaer delegation and allows for the re-use of code.
Initializers called this way, are also known as delegated initializers of the current initializer.

Delegating initialization allows an initializer to skip assigning a value to certain fields, without creating an error that not all fields are initialized.

Calling another initializer can be done by calling another initializer on type `Self` within the current initializer.

> _Note_: The call may also be called directly on the type `Self` represents

If a value would normally be assigned the default value defined for the field, but is explicitly written to within a delegated initializers, the current initializer will not apply the default value to the field.

> _Example_
> ```
> struct Foo {
>     a: i32,
>     b: i32,
>     c: i32 = 3,
> 
>     init fn() {
>         self.a = 1;
>         self.b = 2;
> 
>         // `c` will be implicitly assigned `3` here
>     }
> 
>     init fn(c: i32) {
>         // delegate initialization
>         Self();
> 
>         // Explicitly assign `b` and `c` here, this will result in the assignment to `b` and `c` in `init()` to be skipped
>         self.b = 4;
>         self.c = c;
>     }
> }
> ```

## Fallible initializers [↵](#initializers)

Initializers may be fallible, meaning that they might fail to create a value.

These initializers are marked as either `init?` or `init!`, where the latter may have an additional type provided.
These are called option initializers and result initializers respectively.

These initializers must be called as `T?` and `T!` respecively.

> _Example_
> ```
> struct Foo(i32);
> 
> // Option initializer
> impl Foo {
>     init? fn(val: i32) {
>         if val == 0 {
>             null
>         } else {
>             Foo(0)
>         }
>     }
> }
> 
> // Tries to create `Foo`, but returns `null`
> f := Foo?(0);
> 
> 
> // Result initializer
> impl Foo {
>     init!i32 fn(val: i32) {
>         if val == 0 {
>             .Err(val)
>         } else {
>             Foo(val)
>         }
>     }
> }
> 
> // Tries to create a `Foo`, returns a `i32!Self` type with a value of `.Err(0)`
> f := Foo!(0);
> ```

While fallible initializer still have access to an implicit `self` value, they cannot be used with the [in-place operator], as we cannot guarantee a valid value.
For this same reasons, any assignment to `self` will internally be handled with a special temporary value, which will be returned in the end.

> _Example_
> 
> This means that the following code
> ```
> struct Foo {
>     a: i32,
>     b: i32,
> 
>     init fn(val: i32) {
>         self.a = val;
>         self.b = val + 2;
>     }
> }
> ```
> is equivalent to
> ```
> struct Foo {
>     a: i32,
>     b: i32,
> 
>     init fn() {
>         // Special unitialized `self` value
>         mut self: Self;
> 
>         self.a = val;
>         self.b = val + 2;
> 
>         return self;
>     }
> }
> ```




[`Default`]:         #default-initialization- "Todo: link to docs"
[call expression]:   ../expressions/call-expressions.md
[struct expression]: ../expressions/constructing-expressions/struct-expressions.md
[in-place operator]: ../operators/special-operators.md#in-place-operator-
[composite type]:    ../type-system/types/composite-types.md