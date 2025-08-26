# Reference types
```
<reference-type> := `&` [ 'mut' ] <type>
```

A reference type, like a pointer, points to a given value in memory, but of which the memory is owned by another value.
Copying a reference is a shallow opertion and will only copy of just the pointer to the memory, and any metadata required for dynamically sized types.
Releasing a reference has no effect on the lifecycle of the value it points to, except when referencing a temporary value, then it will keep the temporary value alive during the scope of the reference itself.

References are split into 2 types:

#### Shared reference [↵](#reference-types)

A shared reference prevents direct mutation of the value, but interior mutability provides an exception for this in certain circumstances.
As the name suggets, any mubmer of shared references to a value may exist.

A shared reference is written as `&T`.

#### Mutable reference [↵](#reference-types)

Mutable references (which haven't been borrowed) allow the underlying value to be directly modified.

A mutable reference is written as `&mut T`.

#### Shared xor mutable [↵](#reference-types)

One of the rules references introduce for safety reasons, is that any value can only be shared or mutable at any time, and not at the same time either.
This add a guarantee that any shared reference will contain the same underlying value whenever it is accessed, no matter what other parts of the processors are using it for..
