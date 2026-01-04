# Visibility
```
<vis>      := 'pub' [ '(' <vis-specifier> ')' ]
```

Visibility is used to define the scopes in which an _entity_ ([module], other [item], fields, and variants) can be accessed.

This utilizes the fact that entities form a hierarchical structure of namespaces, i.e. a tree.
Visibility allows a user to choose any subtrees from the greater hierachy in which a given entity will be visible, and allow them to be used in those locations, as long as they contain the subtree in which the entities is defined.

Additionally, any external libraries or packages also count in this hierachy.
This sit on top of each libraries scope mentioned above.

By default, everything is private, except for:
- assocatied items in a `pub` [trait] are public
- variants of a `pub` [enum] (including of [flag enums]) are public

_Example_
```
// A private struct, i.e. will not be accessible outside of hierarchical level
struct Foo;

// A public struct, but with a private field, i.e. only the struct and its implemenations can access the field
pub struct Bar {
   baz: i32,
}

// A public enum, meaning that both `A` and `B` will be accessible where the enum is
pub enum State {
   A,
   B,
}

// A public enum, meaning that `do_something` will be accessible where the trait is
pub trait Trait {
   fn do_something();
}
```

Whether an entity is public or not determines from where an entity can be accessed:
- all entities can be accessed within any sub-entities (descendants) of the entity which contains it
- only public entities can be accessed from an entity higher up the hierarchy (antecessor) which contains it, as long as that entity itself is visible

Utilizing this gives a powerful primitive to control access to both public and private APIs.
Some example cases for this would be the following:
- When developing a library, the developer needs to control the access to only certain parts of each module.
  This can be done by declaring all modules, from the [main module], down to the deepest part of the accessible hierarchy.
  Each non-public entity can then be either kept private, or have a more narrow visible scope defined.
- A set of helper functions could be defined to be available globally within the rest of a module, but not outside of it.
  The helper function can therefore be placed within a sub-module, which is accessible to the rest of the base module, but not outside of it.

> _Example_
> 
> Assuming this is located within the main module or root:
> ```
> // This module is private, preventing any access from outside of the current library.
> // However, since this is located at the root of the library, any code within the library will have access to the helpers.
> mod helper_module {
> 
>    // This function can be accessed anywhere in the library
>    pub fn helper_fn() {}
> 
>    // This function however, is limited to only be used from within the helper module, as it is not defined a visible from the perspetive of any location outside of the current module.
>    fn internal_helper_fn() {}
> }
> 
> // This function is public in the root, so may be accessed from any location that imports the library
> pub fn public_api() {}
> 
> // This module is also public in the root, allowing the library to provide access to any public entity located within the library
> pub mod submodule() {
>    use :.helper_module.
> 
>    // This function is visible from outside the library, as long as it is accessed using the correct path
>    pub fn public_nested() {
>       // Since we privately imported `helper_module` within the current scope, we can access any of its public entities, even when not visible from outside of the library.
>       helper_module.helper_fn();
>    }
> 
>    // Even when in a public module, since the function itself is not declared as public, it cannot be accessed from outside of the library
>    fn private_nested() {}
> }
> ```

## Visibility specifiers [↵](#visibility)
```
<vis-specifier> := 'package'
                 | 'lib'
                 | 'super'
                 | 'in' <simple-path>
```

In addition to any entity being fully public or private, it is also possible to specify the exact scope where an entity will be visible.

This specifier comes in 4 different versions, which limit the visibility of any entity:
- `package`: limit to any libraries that are in the same package as the current library
- `lib`: limit to the entirety of the current library
- `super`: limit to the scope containing the current entity
- `in <simple-path>`: limit to the scope defined by `<simple-path>`. If the path has no path start, it is relative to the scope the current entity is located in.

## Common denominator [↵](#visibility)

In locations where a visibility may conflict with another visibility, it will be narrowed down to the narrowest visibility, also known as the common denominator
This common denominator is decided by the first visibility that is in the following order:
1) 'private' or no visibility specifier
2) `in path` or `super`. If both paths happen to be one of these paths, do the following:
   1) if either is `super`, replace it with the library relative path of the direct outer scope
   2) if either is `in path`, expand the path into its library relative path
   3) if neither path is the root of the other, the visibility will be private
   4) otherwise, pick the non-root path
3) `lib`
4) `packag#`
5) `pub` without a specifier

> _Note_: This is mainly used by [use fields].



[item]:       ./items.md
[module]:     ./items/modules.md
[trait]:       ./items/traits.md
[main module]: ./package-structure.md#main-module-
[enum]:        ./type-system/types/composite-types/enum-types.md
[flag enums]:  ./type-system/types/composite-types/enum-types.md#flag-enums-
[use fields]: ./type-system/types/composite-types/struct-types.md#use-fields-