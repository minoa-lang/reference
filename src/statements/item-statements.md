# Item statements
```
<item-stmt> := <use-item>
             | <fn-item>
             | <type-alias-item>
             | <struct-item>
             | <union-item>
             | <enum-item>
             | <flag-enum-item>
             | <bitfield-item>
             | <const-item>
             | <static-item>
             | <impl-item>
             | <assert-item>
```

An items statement allows for the declaration of an item within a block, this only includes a subset of allowed items.

Declaring an item with a block will restrict its scope to the containing scope, and does not allow it to be referenced from anywhere outside this scope.
Thse items do not have a so-called _canonical path_, nor have any sub-items declared in them.

An item contained within a scope may access the items that are available at that location, but cannot access any local variable that are defined within the same scope.

> _Example_
> ```
> fn outer() {
>     a := 1;
> 
>     fn inner() {
>         // error: an inner item cannot access an item declared outside of itself
>         // b := a;
>     }
> 
>     inner();
> }
> ```

However, they can access any constant value defined in an outer scope, unless they are shadowed by an inner variable with the same name.

> _Example_
> ```
> fn outer(const T: type, a: i32) {
> 
>     fn inner() {
>         // Can access `T` here, since it's a constant value
>         let b : T = ...;
>     }
> }
> ```

The allowed items additionally come with some restrictions:
- [`impl` items] are only allowed to implement functionality on local types, types which are defined in the current scope.


[`impl` items]: ../items/implementations.md