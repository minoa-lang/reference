# Item statements
```
<item-stmt> := <item>
```

An items statement allows for the declaration of an item within a block.

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

_Example_
```
fn outer(const T: type, a: i32) {

    fn inner() {
        // Can access `T` here, since it's a constant value
        let b : T = ...;
    }
}
```
