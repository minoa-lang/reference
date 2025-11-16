# Call expressions
```
<fn-call> := <expr> [ '?' ] <fn-args>
<fn-args> := '(' <fn-arg> { ',' fn-arg }* [ ',' ] ')' [ <trailing-closures> ]
           | <trailing-closures>
<fn-arg>  := [ <ext-name> ':' ] <expr>
```

A call expressions calls a function.

The expression will complete when and if the function returns.
Meaning that if the function returns a [never type], this expression will never complete.
Otherwise, if the function returns a value, the expression will evaluate to this expression, meaning that the function determines whether the expression resuls in a [place] or [value] expression.

The left-hand expression of a call expression must be one of the following types:
- a [function item]
- a [pointer] or [reference] to a [raw function type]
- a type with an [initializer] (i.e. to call its initializer)
- a value of a type implementing one of the following traits:
  - [`Fn`]
  - [`FnMut`]
  - [`FnOnce`]
  - [`AsyncFn`]
  - [`AsyncFnMut`]
  - [`AsyncFnOnce`]


If needed, an automatic borrow of the left-hand expression will be taken.
It can also be dereferenced automatically, if needed.

Whenever a parameter in the called function has a label defined, the argument passed must have an identical label, and may not be left out.

Any default parameter do not have to be passed, if these are not supplied, their default value will be automatically passed to the function.

> _Example_
> ```
> fn foo(_ a: i32, _ b: i32) {}
> 
> foo(1, 2);
> 
> // with labels
> fn bar(a: i32, b: i32) {}
> 
> bar(a: 1, b: 2);
> 
> // with default values
> fn baz(_ a: i32, _ b: i32, _ c: i32 = 3);
> 
> baz(1, 2);
> // is equivalent to
> baz(1, 2, 3);
> 
> // call on a closure, which implements `Fn`
> fn{ ... }();
> ```

> _Note_: Arguments to lazy parameters are essentially automatically generated closures generated around the expression in that argument's place.

## Optional chaining [↵](#call-expressions)

Similar to an [optional field access], the call expression supports optional chaining, also known as a null-propagating call.
This means the call only happends then the left-hand expression is a value that does not have an erronous value.

This operation is handled by the `OptAccess` trait and works on all types implementing it.

_Example_
```
fn foo(_ a: i32, _ b: i32) {}

foo(1, 2);

opt_foo: ?*fn(i32, i32) = null;

// the `null` value is propagated and the call is skipped
opt_foo?(1, 2);
```

## Trailing closures [↵](#call-expressions)
```
<trailing-closures> := <trailing-closure> { <name> ':' <trailing-closure> }*
<trailing-closure>  := <closure-body>
```

A trailing closure expression is a special syntax, allowing closures being passed to a function to appear after all other arguments of the function, i.e. as the function's final parameters.
The first of these is the only one that is allowed to be [labelless], all following closures must have an explicit label.

These closures can have their `fn` keyword left out.

Whenever a function receives multiple trailing closure, it will base the first closure to pass on the label for the second closure,
i.e. if the second trailing closure matches the label of argument `N`, the non-labelled closure will be passed to argument `N - 1`.
Each subsequent trailing closure after the first one, must be preceeded with the associated label of that argument.

> _Example_
> ```
> fn foo(a: i32, c0: fn(), c1: fn(), c2: fn());
> 
> // pass the trailing closure to `c2`, `c1` must be explicitly passed
> foo(a: i32, c0: fn{}, c1: fn{}) { ... }
> 
> // pass the trailing closures to `c1` and `c2` respectively
> foo(a: i32, c0: fn{}) { ... } c2: { ... }
> 
> // error: no value is passed to `c0`, as the first trailing closure is assigned to `c1`
> foo(a: i32) { ... } c2: { ... }
> ```

If a function only takes in closures and they are all provided as trailing closures, the parentheses for the function may be left out.

> _Example_
> ```
> fn bar(a: fn(i32, i32) -> bool);
> 
> bar() { $0 < $1 };
> // may also be called as
> bar { $0 < $1 };
> ```

## Calling methods as functions [↵](#call-expressions)

All methods can be called as if they were functions.
To do this, the receiver is passed as the first argument.

The `self` argument is the only argument where the label is optional, meaning it can be passed simply as a value, or with a `self` label.

Calling a method this way can also be used to prevent either automatically borrowing or dereferencing of its caller.

> _Example_
> ```
> trait Bar() {
>     fn(&self) bar(a: i32, b: i32)
> }
> 
> struct Foo {
>     fn(&self) bar(_ a: i32, _ b: i32) {}
> 
>     impl as Bar {
>         fn(&self) bar(_ a: i32, _ b: i32) {}
>     }
> }
> 
> foo := Foo{};
> 
> foo.bar();
> // can also be called in the following ways
> 
> // - without label
> Foo.bar(&foo, 1, 2);
> 
> // - with label
> Foo.bar(self: &foo, 1, 2);
> 
> // - call `Foo`'s implmentation of `Bar.bar`
> Foo.(Bar.bar)(&foo, 1, 2);
> ```


[`Fn`]:                  #call-expressions "Todo: link to docs + ensure correct trait name"
[`FnMut`]:               #call-expressions "Todo: link to docs + ensure correct trait name"
[`FnOnce`]:              #call-expressions "Todo: link to docs + ensure correct trait name"
[`AsyncFn`]:             #call-expressions "Todo: link to docs + ensure correct trait name"
[`AsyncFnMut`]:          #call-expressions "Todo: link to docs + ensure correct trait name"
[`AsyncFnOnce`]:         #call-expressions "Todo: link to docs + ensure correct trait name"
[place]:                 ../expressions.md#place-expressions-
[value]:                 ../expressions.md#value-expressions-
[optional field access]: ../expressions/field-access-expressions.md#optional-chaining- "Todo: section does not exists yet"
[function item]:         ../items/functions.md
[labelless]:             ../items/functions.md#parameter-labels-
[initializer]:           ../items/initializers.md
[never type]:            ../type-system/types/builtin-types/never-types.md
[raw function type]:     ../type-system/types/function-like-types/raw-function-types.md
[pointer]:               ../type-system/types/pointer-like-types/pointer-types.md
[reference]:             ../type-system/types/pointer-like-types/reference-types.md