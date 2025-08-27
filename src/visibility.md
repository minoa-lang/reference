# Visibility
```
<vis> := 'pub' [ '(' <vis-kind> ')' ]
<vis-kind> := 'package'
            | 'lib'
            | 'super'
            | 'in' <simple-path>
```

name resolution operates on a global hierarchy of names scopes.
Each level in the hierarchy can be seen as an item, this inludes items defined in the current library, but also those elsewhere in the package or external packages.

To control whether these can be used from accoress different locations, each item is checked for its availability in other scopes and whether these can be used or not.
If it is not available due to the items visibility, a compile error will be generated.

By default, everything is private, with 2 exceptions:
- Associated items in a `pub` trait are public
- Enum variants of a `pub` enum are also public by default.

When an item is declared as `pub`, it can be thought of as being accessible to the outside world.

With the notion of an item being private of public, items can be accessed in 2 cases:
- If an item is public, then it can be accessed externally from some module `m` if you can access all the item's ancester modules from `m`
   YOu can also potentially be able to name the item through re-exports.
- If an item is private, it may be accessed by the current module and its descendants.

## Specifiers [↵](#visibility)

In addition to purely having items being purely private or public, items can also have a visibility spanning a specific scope, this is done with a specifier.
The following specifiers are available:
- `pub(package)`: Makes items visible inside of the current package
- `pub(lib)`: Makes item visible inside of the current library (no equivalent exists for binaries, as `pub` has the same effect in them, as they do cannot be used by another artifact)
- `pub(super)`: Makes the item visible inside of the parent's named scope.
- `pub(in path)`: Makes the item visible to the path specified (path is relative to the current artifact)

## Common denominator [↵](#visibility)

The common denominator in mainly used for:
- [struct use fields]

The common denominator of 2  visibilities is decided by choosing the narrowest visibility.
Meaning the visibilities will be decided by the first visibility in the followin order
1) 'private', i.e. no visibility specifier
2) `pub(in path)` or `pub(super)`
3) `pub(lib)`
4) `pub(package)`
5) `pub`

When the visibilities are both either `pub(in path)` or `pub(super)`, the following is done:
- convert the `super` specifier to an `in path` specifier
- pick the common root of the path
- If no common path is available, the visibility will be 'private'



[struct use fields]: ./type-system/types/struct-types.md#use-fields-