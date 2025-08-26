# Interior mutability

Sometimes a type needs to be mutated while having multiple aliases.
This can be achieved using a concept called _interior mutability_.
A type has interior mutability if its internal state can be modified from a shared reference to it.
This goes against the usual requirement that the value pointed to by a shared reference is not mutated.

`UnsafeCell<T>` is the only way of disabling this requirement.
When `UnsafeCell<T>` is immutably aliased, it is still safe to mutate or obtain a mutable reference to the `T` it contains.
As with all other types, it is undefined behavior to have multiple `&mut UnsafeCell<T>` aliases.

Other types with interior mutabiliity can be created using `UnsafeCell<T>` as a field.

> **Warning**: The programmer must ensure that this does not cause any unininted consequences or may cause other undefined behavior.
