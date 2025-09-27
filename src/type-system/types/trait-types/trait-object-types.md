# Trait object types
```
<trait-object-type> := 'dyn' <trait-bound>
```

A trait object type is an opaque type that implements a set of traits, meaning that they are `?Sized` by default.
A trait object exists out of a combination of [object safe traits] and [marker traits].

In addition, a trait object will also implement all [supertraits], as these are by definition required to implement the traits.

Different trait objects may alias each other if the traits match, but are in different orders, meanign that `dyn A & B & C` is the same as `dyn C & A & B`.

A trait object can be assigned to any less specific trait object, meanig that the assigned value is of a type that has less of the trait bounds and supertraits supplied to the triat object.
Converting between traits however comes with some additional overhead to possibly build up the vtable, if this cannot be resolved at compile time.

Since a trait object cannot know what type is stored in it, a trait object type is a [dynamically sized type], meaning it needs to be stored behind an indirection.

Trait object types allow for "late bindings" in cases where the type being used cannot be known at compile time, but it knows the functionality the type is supposed to posses.
Calling a method will use virutal dispatch of a method: that is, the function pointer is loaded from the vtable, and is then invoked indirectly, incurring an additional pointer indirections by requiring the function to be looked up in the vtable.

## Internal representation [↵](#trait-object-types)

Trait objects are stored as 'fat pointers', which consist out of 2 components:
- a pointer to an object of type `T` which implements the given traits.
- a pointer to a vtable, which contains a pointer to type info, and a list of pointers to the methods of the traits and their supertraits, of `T`'s implementation

The actual implementation of each vtable may vvary on an object-by-object basis.

> _Implementation note_: Behind the scenes, these types are hidden behind a compiler generated [auto-traits] which has all these traits as supertraits.



[dynamically sized type]: ../../dynamically-sized-types.md
[object safe traits]:     ../../../items/traits.md#object-safety-
[marker traits]:          ../../../items/traits.md#marker-traits- "Todo: Section does not exists yet in traits.md"
[supertraits]:            ../../../items/traits.md#supertraits-
[auto-traits]:            ../../../items/traits.md#auto-traits- "Todo: Section does not exists yet in traits.md"