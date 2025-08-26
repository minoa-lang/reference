# Impl trait types
```
<impl-trait-type> := 'impl' <trait-bound>
```

An impl trait type introduces an unnamed generic parameter that implements the given intrefaces to the item it is used in.
It can appear in only 2 locations: function paramters (where it acts as an anonymous type of the parameter to the function) and function return types (where it acts as an abstract return type).

#### Anonymous type parameter [↵](#impl-trait-types)

A function can use an impl trait type as the type of its parameter, where it declares the parameter to be of an anonymous type.
The caller must provide a type that statisfies the bounds declared in the anonymous type paramter, and the function can only use the functionality available through the trait bounds of the anonymous type paramter.

An example of this would be:
```
trait Trait {}

// Generic type parameter
fn with_generic_type<T is Trait>(param: T) {}

// impl trait typed paramter
fn with_impl_type(param: impl Trait) {}
```

This can be seens as synctactic sugar for a generic type paramter like `<T is Trait>`, except that the type is anonymous and does not appear within the generic argument list.

> _Note_: For function arguments, generic type parameters and `impl Trait` are not completely equivalent
> With a generic type paramter `<T is Trait>`, the caller is able to explicitly specify the type of the generic type parameter `T` when calling the function.
> If an `impl Trait` is used, the caller cannot ever specify the type of the parameter when calling the function.
>
> Therefore, changing between these types within a function signature should be considered a breaking change.

#### Abstract return types [↵](#impl-trait-types)

A function can use an impl trait type as the type in its return type.
These types stand in for another concrete type wher the caller may only used the functinality declared by the specified traits.
Each possible return type of the function must resolve to the same concrete type.

An `impl Trait` in the return allows to return a abstract type that does not have to be stored within dynamic memory.
This can be particularly usefull when writing a function returning a closure or iterator, as for example, a closure has an un-writable type.

Without this functionality, it would only be possible to return a 'boxed' type:
```
fn returns_closure() -> Box<todo> {
    Box::new(|x| x + 1)
}
```

This could incur performance panalties from heap allocation and dynamic dispatching.
However, using this type, it is possible to write it as:

```
fn returns-closure -> impl todo {
    |x| x + 1
}
```

Which avoids the drawbacks of the 'boxed' type.

_TODO: add note on (memory) effect implications_

#### Abstract return types in trait declarations [↵](#impl-trait-types)

Functions in traits may also return an abstract return types, this will create an anonymous associated type within the trait.

Evety `impl Trait` in the return type of an associated function in an trait is desugared to an anonymous associated type.
The return type that appears in teh implementation's funciton signature is used to determine the value of hte associated type.

##### Differences between generics and `impl Trait` in a return [↵](#abstract-return-types-in-trait-declarations-)

When used as a type argument, `impl trait` work similar to the semantics of generic type parameters.
But when used in the return, there are significant changes, as unlike with a generic parameter where the caller can choose the return type, the implementation chooses the function's return type.

For example, the function
```
fn foo<T is Trait>() -> T { ... }
```
Allows the caller to determine the return type.

In contrast, the function
```
fn foo() -> impl Trait { ... }
```
doesn't allow the caller to explicitly determine the return type.
Instead the function chooses the return type, with the only guarantee that it implements the required traits.

#### Impl trait limitations [↵](#impl-trait-types)

An impl trait type may only occur for non-`extern` functions.
It can also not be the type of a variable declaration, a field, or appear inside a type alias.
