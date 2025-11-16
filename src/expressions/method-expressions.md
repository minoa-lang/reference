# Method expressions
```
<method-call-expr> := <expr> [ '?' ] '.' [ <ext-name> | <path-disambig> ] [ '?' ] <fn-args>
```

Methods calls are used to call any associated method of a type, either as direct methods or as trait method implementations.

When the method can be resolved at compile time, it will result in a static dispatch, otherwise it will use dynamic dispatching to call the function.
Generally, this is decided by whether the left-hand expression that holds a [trait object] or not, where the first will result in dynamic dispatching.

When calling the method, the receiver can be automatically dereferences or borrowed, the exact process is specified [below].

The arguments provided to the methods works similarly to as is defined by the [call expression].

> _Example_
> ```
> struct Foo {
>    fn bar();
> }
> 
> foo := Foo{};
> foo.bar();
> ```

## Optional chaining [↵](#method-expressions)

Similar to an [optional field access], the method expression supports optional chaining, also known as a null-propagating method call.
This means that the method call only happens when the receiver is a value that does not have an erronous value.

This operation is handled by the `OptAccess` trait and works on all receiver types implementing it.

> _Example_
> ```
> struct Foo {
>    fn(&self) bar() -> i32 { 42 }
> }
> 
> a := Foo{};
> a: Foo = null;
> 
> // always executes
> assert(foo.bar() == 42);
> 
> // the `null` value is propagated and the method call is skipped
> assert(foo?.bar() == null);
> ```   

## Optional methods [↵](#method-expressions)

In addition to optional chaining, it is also possible for a function being called to not exists.
This is only possible when implementing a trait which has an [optional trait method].

If this function is not provided, the function will result in a `null` value, as these function always return `?T`, where `T` is the return type of the function.

> _Example_
> ```
> trait Trait {
>    fn Foo?();
> }
> 
> struct Bar {
>    impl as Trait {
>       fn Foo() {}
>    }
> }
> 
> struct Baz {
>    impl as Trait {
>    }
> }
> 
> bar := Bar{};
> baz := Baz{};
> 
> // `foo` is implemented, this will call it and return a value of `.Some(())`
> bar.foo?();
> 
> // `foo` is not implemented, so this will not call it, and return a `null` value
> baz.foo?();
> ```

## Method lookup [↵](#method-expressions)

When looking up a method to call, the receiver may be automatically dereferences or borrowed in order to call that method.
This result in a more complex lookup relative to regular function calls, where only it's arguments need to be taken into account for.

For methods, looking up the method is done using the following precedure:

1. Build a list of candidate receiver types:
   1. Add the type of the receiver to the list
   2. Obtain, by repeatedly dereferencing the receiver's type, each possible type to the candidate list
   3. Finally, attempt an unsized corecion and add the resulting type to the candidate list, if it was successful.
   4. For each type `T` in the list, add `&T` and `&mut` to the list directly after `T`
2. For each candidate `T`, search for a visible method with a receiver of that type in the following places:
   - `T`'s inherent methods (methods directly implemented by `T`, or extended from a trait implementation)
   - Any of the methods of any current visible interface implemented by `T`.
     If `T` is a type parameter, methods provided by interface bounds on `T` are looked up first.
   - All remaining method scopes
3. Pick the methods matching the arguments

> _Note_: For mor detailed info about argument resolution and conflixts, check the [function label pseudo-overloading section].

If this results in multiple candidates:
- if there is only 1 inherent method, use that
- if there are no inherent methods, return an error.
  The receiver must either be converted to the appropriate type, or the method call should be called using a [trait disambiguation].

The lookup does not take either the mutability of the receiver, nor any attribute that might affect where the method can be called.
If the selected is unable to be called for any of these reasons, it will result in an error after this process is finished.



[below]:                                     #method-lookup-
[call expression]:                           ./call-expressions.md
[optional field access]:                     ./field-access-expressions.md#optional-chaining- "Todo: section does not exists yet"
[trait object]:                              ../type-system/types/trait-types/trait-object-types.md
[optional trait method]:                     ../items/functions.md#trait-functions--methods-
[function label pseudo-overloading section]: ../items/functions.md#label-based-psuedo-overloading-
[trait disambiguation]:                      ../identifiers-paths.md#trait-disambiguation-