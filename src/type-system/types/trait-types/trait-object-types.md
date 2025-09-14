# Trait object types
```
<trait-object-type> := 'dyn' <trait-bound>
```

A trait object type is an opaque type that implements a set of traits, any set of traits is allowed, except of an opt-in trait like `?Sized`.
The objects are guaranteed to not only implement the given traits, but also their parent traits.

Different trait objects may alias each other if the traits match, but are in different orders, meaning that `dyn A & B & C` is the same as `dyn C & A & B`

A trait object can be assigned to any less specific trait objects, meaning that it can be assgined to a type that has less trait bounds.
This *may* incur some additional overhead, as a new vtable needs to be retrieved and assigned, if this cannot be determined at compile time.

Due to the opaqueness of trait objects, this type is dynamically sized, meaning that it must be stored behind a reference, a pointer, or a type accepting DTSs.

Trait objects are stored in so-called "fat pointers' which consists out of 2 components:
- A pointer to the an object of a type `T` that implements the trait bounds
- A virtual table, also known as a vtable, which contains both RTTI info and a list of function pointers to the methods of the traits and their parent types, of `T`'s implementation.

Trait object types allowe for "late binding" in cases where the types being used cannot be known at compile time, but the programmer knowns the functionality they posses.
Calling a method will use a virtual dispatch of the method: that is, teh function pointer is loaded from the vtable, and is then invoked indirectly, incurring a pointer indirection.
The actual implementation of each vtable may vary on an object-by-object basis.
