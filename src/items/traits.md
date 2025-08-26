# Traits
```
<trait-item> := { <attribute> }* [ <vis> ] [ 'unsafe' ] [ 'sealed' ] 'trait' <name> [ <generic-params> ] [ ':' <trait-bound> ] [ <where-clause> ] '{' { <trait-item> }* '}'
```

A trait represents an abstract interface that type can implement, containing zero or more trait items, like:
- [functions & methods](./functions.md#trait-functions--methods-)
- [type aliases](./type-aliases.md#trait-type-aliases-)
- [constants](./consts.md#trait-constant-)
- [properties](./properties.md#trait-properties-)

All traits define an implicit `Self` type, and refers to "the type that is implementing this trait".
Any generic parameters applied to the trait, are also passed along to the `Self` type

Traits can be implemented via individual implementations.

A trait can be defined as `sealed`, this means that the trait can only be implemented from the current library and any implementation outside of the current library will result in a compile error.

## Object safety [↵](#trait)

Object safety specifies a set of requirements that the trait needs to adhere to to be allowed to be used in places where a trait object type is allowed.
These are:
- All supertraits must be object safe.
- The trait cannot be sized, i.e. it may not requires `Self is Sized`.
- It must not have associated constants.
- It must not have associated types using generics.
- All associated functions must either be dispatchable from a trait object or be explicilty non-dispatchable.
    - Dispatchable functions must adhere to:
        - Not have any generic parameters.
        - Method is only allowed to use the `Self` type within the receiver.
        - The receiver needs to allow for dynamic dispatch, e.g. `&self` or `&mut Self`, and types implementing `DispatchFromDyn`.
        - Parameters and return types must not be an inferable type, meaning they may not be an impl trait type.
        - May not have a sized bound on the receiver (`Self is Sized` implies this).
    - Explicit non-dispatchable functions require:
        - Have a sized bound on the receiver (`Self is Sized` implies this).

## Supertraits [↵](#trait)

A 'super trait' is a trait that is required to be implemented by a type to implement a specific trait.
Anywhere a generic or interface object is bounded by a trait, it is also bound by that trait's supertraits.

Supertraits are declared as a trait bound on the `Self` type, and transitively the supertraits of traits declared in their trait bounds.
They can either be defined as a bound directly on the trait, or to `Self` in a where clause.
A trait cannot be its own supertrait, and they cannot form any cyclical supertrait dependence.

## Unsafe traits [↵](#trait)

Traits can be declared as `unsafe`.
Unsafe traits come with additional requirements that the programmer needs to guarantee is being followed follow.

## Visibility [↵](#trait)

Traits define their visiblity directly on the trait itself, and all items within the trait take on that visibility.
Individual associated items cannot declare their own visibility.

## Trait Items [↵](#trait)

```
<trait-item> := <trait-func>
              | <trait-type-alias>
              | <trait-const>
              | <trait-property>
```

Trait items are items that are assocated with a trait.
The following items are supported inside a trait:
- functions
- type aliases
- constants
- properties

Any item that does not have a default value or implementation is required to be implemented in any trait implementation.
