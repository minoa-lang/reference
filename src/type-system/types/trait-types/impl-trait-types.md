# Impl trait types
```
<impl-trait-type> := 'impl' <trait-bound>
```

An impl trait type is used to introduce any type that implemenets the given set of traits to be used in its place.

= It can appear in only 2 locations: as a function parameter (where it acts as an inferred type parameter) and function return types (where it acts as an abstract return type).

## Anonymous type parameter [↵](#impl-trait-types)

A function can use an `impl` trait type as the type of its parameters, where it declares the parameter to be of an anonymous type.
The caller must provide a type that satisfies the bounds declared in the anonymous type parameter, and the function can only use the functionality available through the trait bound of the anonymous type parameter.

> _Example_
> ```
> trait Trait {}
> 
> // Inferred type parameter
> fn with_generic_param[T is Trait](param: T) {}
> 
> // impl trait type parameter
> fn with_impl_type(param: impl Trait) {}
> ```

This can be seen as syntactic sugar for a inferred type parameter like `[T is Trait]`, except that the type is anonymous and can not be used within the function, as it does not explicitly appear as either an inferred or regular type parameter.

## Abstract return types [↵](#impl-trait-types)

A function can use an impl trait type as the type in its return type.
These types stand in for another concrete type where the caller may only use the functionality declared by the specified traits.

It allows a function to return an abstract type that does not have to be stored with dynamic memory.
This can be particulary useful when writing a function returning for example an iterator, as these names can become very long when nested iterators are produced.

> _Example_
> Without this functionality, it would only be possible to return a that is stored somewhere in memory
> ```
> fn returns_iter() -> Heap<dyn Iterator> {
>     ...
> }
> ```
> This could incur performance penalties for the heap allocation and dynamic dispatch.
> However, using this type, it is possible to write it as
> ```
> fn returns_iter() -> impl Iterator {
>     ...
> }
> ```

Since this return type only guarantees that a return type will adhere to the given traits, and provides no info about the actual type,
it is possible to return different types that match this requirement.

If only a single type is returned, the return type will just be a stand-in for that type.

If multiple different types are returned, the return type can be interpreted to be an [adhoc enum], which will dispatch the relavent functions.
This does require that any associated types between these multiple return types match, as a mismatch can cause issues in later calls.
This can be seen as having a similar overhead to dynamic dispatching from a [trait object type].

Since there is not guarantee whether this would return a single type or a generated [adhoc enum], abstract return types are limited to only calling function which would be allowed on an [object safe] trait implementation.

> _Implementation note_: For the pupose of type inference, this provides enough info to be able to type check.
> However, when it comes to lowering to MIR, it first needs to be converted depending on the use-case, as mentioned above.

### Abstract return type and `Self` returns [↵](#abstract-return-types-)

When calling function on an abstract return type, which depends on a `Self` type, it is only possible to call a function returning one of the following:
- A non-`Self` type which wraps `Self`, which can guarantee a fixed set of capabilities, but only those guaranteed by the type contraint provided by the trait type in the abstract return type.
- A `Self` type, which will then instead of being a concrete type, will the same as the abstract return type

> _Example_: `Self` return type
> 
> Assuming the following code
> ```
> trait Foo {
>     fn foo() -> Self;
> }
> 
> fn bar() -> impl Foo {
>     ...
> }
> ```
> If we were to call `bar().foo()`, we get a call which would normally return a concrete type.
> Abstract return types break that, since there is no guarantee what is return by `bar()`, there is also no way of knowing what the type of calling `foo()` will be.
> 
> For this reasons, whenever this situation happens, the second call gets inferred as returning a `impl Foo` instead of the type implementing `Foo`.

> _Example_: Wrapped `Self` return type
> 
> Assuming the following code
> ```
> trait Foo {
>     fn foo() -> Wrapper(Self);
> }
> 
> trait Bar {}
> 
> struct Wrapper[T] {
>     val: T,
> 
>     fn bar() -> i32 { 42 }
>     fn baz() -> impl Foo where T: Foo { &val }
>     fn quux() -> i32 where T: Bar { 2 }
>     fn quuz(const type: T) -> i32 { 3 }
> }
> 
> struct A {
>     impl Foo() {
>         fn foo -> Wrapper(A) { ... }
>     }
> }
> struct B {
>     impl Foo() {
>         fn foo -> Wrapper(B) { ... }
>     }
>     impl Bar {}
> }
> 
> fn return_impl(toggle: bool) -> impl Foo {
>     if toggle {
>         A{}
>     } else {
>         B{}
>     }
> }
> ```
> This would allow us to call following:
> - `bar()`, which is always available and would also break the dependency on an abstract return type
> - `baz()`, which is available, as `impl Foo` fulfills the `Foo` bound, but this return type is still dependent on `Foo`
> 
> But we cannot call:
> - `quux()`, as `impl Foo` can never implement `Bar`, even if `B` fulfills to it, since we don't have this info at the point of calling `quux()`
> - `quuz()`, as it does not adhere to a function that can be called by an object safe trait

### Abstract return type in trait declarations

Functions in traits may also return an abstract return type.
These will be bound to a special anonymous associated type which will be used when checking for compatibility in any return type, as menioned [above](#abstract-return-types-).

## Impl trait limitations

An impl trait type may only occur for a non-`extern` and non-`export` functions.

Values with a impl trait type may also not be used in the following locations:
- field types
- properties



[trait object type]: ./trait-object-types.md
[adhoc enum]:        ../composite-types/enum-types.md#adhoc-adt-enums-
[object safe]:       ../../../items/traits.md#object-safety-
