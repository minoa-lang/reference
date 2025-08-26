# Implementations
```
<impl-item> := <inherent-impl> | <trait-impl>
```

An implementation is an items that associates items with an implementing type.
There are 2 types of implementations:

## Inherent implementation [↵](#implementations)

```
<inherent-impl> := { <attribute> }* [ <vis> ] [ 'unsafe' ] 'impl' [ <generic-params> ] <type> [ <where-clause> ] '{' { <assoc-item> }* '}'
```

An inherent implementation is defined without specifying a trait to implement.
The type implementing is called the _implementing type_ and the associated items are the _associated items_ of the implementing type.

Inherent implementations associated the contained items ot the implementing type.
Inherent implementaions can support associated functions (including methods), properties, and constants.

The path to an associated item is the path to the implementing type, followed by the associated item's identifier as the final component of the path.

A type can also have multiple inherent implementations.
An implementation for a type must be defined in the same library as the original type definition.

If a visibility attribute is defined for the block, all items with in the block will default to that visibility.
If `unsafe` is added to the block, then all functions within the block will be marked as unsafe.

## Trait implementation [↵](#implementations)

```
<trait-impl> := { <attribute> }* [ 'extend` ] [ 'unsafe' ] 'impl' [ <generic-params> ] <type> 'as' <trait-path> [ <where-clause> ] '{' { <assoc-item> }* '}'
```

A `trait` implementation is defined like an inherent implementation, but also include the trait to be implemented.

The trait is known as the _implemented trait_, and the _implementing type_ implements the trait.

A trait implementation must define all non-default associated items declared by the implemented trait and it can redefine (i.e. override) an item that has a default implementation.
It is not allowed to define any implementation that is not defined in the implemented trait.

Unsafe traits require the `unsafe` keyword to be added to the implementation.
`trait` implementations are not allowed to specify any visibility for items.

By default, items within a trait implementation cannot be accessed directly and need to be accessed using a trait disambiguation.
Adding `extend` to the implementation allows the items to be directly accessed.

### Coherence

A trait implementation is coherent when it can be be defined within the current library.

A trait implementation is considered coherent if either the below rules are followed, or there are overlapping implementations.

Two trait implementations overlap when there is 2 implementations ca be instantiated for the same type.

The coherence rules require that the implementation `impl<P0..=Pn> T0 as Trait<T1..=Tn>` to adhere to one of the following:
- `Trait` is a local trait
- At least one type `T0..=Tn` must be a local type 

> _Note_: Coherence rules might be changed in the future

## Impl field items [↵](#implementations)
```
<impl-field-items>    := <inherent-impl-field> | <trait-impl-field>
<inherent-impl-field> := [ 'vis' ] [ 'unsafe' ] 'impl' [ <generic-params> ] <type> [ <where-clause> ] '{' <assoc-item> '}'
<trait-impl-field>    := [ 'extend' ] [ 'unsafe' ] 'impl' [ <generic-params> ] <type> [ <where-clause> ] '{' <assoc-item> '}'
```

Impl field items are equivalent to their corresponding implementation item, but can be directly declared as an associated item in an item.

## Associated items [↵](#implementations)
```
<assoc-item> := <fn-item>
             | <method>
             | <const-item>
             | <static-item>
             | <impl-field>
             | <struct-item>
             | <union-item>
             | <enum-item>
             | <bitfield-item>
             | <init-item>
```

Associated items are items which may appear either in distinct implementations, or inline within type bodies, where they act as if they are in an impl.
The following items are allowed:
- Functions
- Methods
- Type aliases
- Statics
- Constants
- Properties

These items can be accessed from the type they are implemented on, below `Item` represents the item that is implemented, `Type` represent the type that has the item implement for it, and `Trait` is the trait the `Type` might be implemented.
- If the type is a path-like type: `Type.Item`
- If the type is not a path type: `(Type).Item`
- If the type implements a trait, which is not marked as `extend`: `Type.(Trait.Item)`
