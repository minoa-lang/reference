# Pointer types
```
<pointer-type> := '^' [ <ptr-specifiers> ] <type>
                | '[' '^' [ ';' <expr> ] ']' [ <ptr-specifiers> ] <type>
<ptr-specifiers> := [ <ptr-align> ] [ 'allowzero' ] [ 'mut' | 'volatile' ]
<ptr-align> := 'align' '(' <expr> ')'
```

A pointer type represents an address in memory containing hte underlying type.
Copying or dropping pointer has no effect on the lifecycle of any value.
Derefencing a pointer is an `unsafe` operation.

Although raw pointers are not neccesarily discouraged, they do lack the safety guarantees that are associated with references.
Meaning anything can happen with the underlying memory, like being modified by another part of the processor.
It is therefore encouraged to use references over pointers whenever possible.

Raw pointer are generally discourages, and are mainly there to allow for interopterability and perfomance-critical and low-level functionality.
It is preferable to use smart pointer wrapping the inner value.

When comparing pointers, they are compared by their address, rather than what they point to.
When comparing pointers to dynamically sized types, they also have their additional metadata compared.

A pointer cannot contain a 'null' value when not in an optional type.

Minoa has 3 kinds of pointers.

> _Todo_: Should pointers handle ownership, some language item, or should it be something API based?
 
#### Single element pointers [↵](#pointer-types)

A single element pointer `^T` or `^mut T`, refers to exactly 1 element in the memory pointed to.

This pointer can be converted to a reference by re-borrowing it using `&^` or `&mut ^`.

Single element pointers do not allow any arithmetic between pointer and integers, and therefore only support subtracting pointer from eachother, i.e. `ptr - ptr`.
Single element pointers are allowed to be sliced to a single element slice.

As an example, the pointer `^i32` would represent a pointer to a single immutable `i32` value.

#### Multi element pointers [↵](#pointer-types)

A multi-element pointer `[^]T` or `[^]mut T`, pointing to an unknown number of elements.

In addition to the operations allowed on a single element pointer, a multi-element pointer allows additional functionality:
- pointer and integer arithmetic
- slicing with any range
- indexing of the underlying memory

When a pointer contains dynamically sized types, it will consist out of an array of fat pointers.

Multi-element pointers require a type with an known size and alignment, meaning that multi-element pointer of dynamically sized types are not allowed.

Unlike slices, mutli-element pointers do not support safety checks.

As an example, the pointer `[^]i32` would represent a pointer to an unknown number of immutable `i32` values.

#### Sentinel-terminated pointers [↵](#pointer-types)

A sentinel terminated pointer `[^;N]T` or `[^;N]mut T` is very similar to a multi-element pointer.
The main difference lies in the fact that a sentinel terminated pointer will only contain the number of elements until the first occurance of the sentinel value.

The main purpose of this type is to prevent buffer overflows when interacting with C-style and OS code.

#### Volatile pointers [↵](#pointer-types)

Pointers operations are generally assumed not to have any side-effects, and the optimizer uses that fact to be able to optimize the code.
An issue occurse when the pointer points to memory that does have side effects, like MMIO (Memory Mapped I/O).

To handle this correctly, `volatile` pointers can be used. They are pointer that are allowed to have side-effects.
Both single and multi-element pointers support volatile.
`volatile` implies `mut`.

> _Note_: `volatile` pointers are unrelated to atomics, in usecases where `volatile` is used with atomics, this is most likely in error.

#### Alignment [↵](#pointer-types)

Each type has an alignment as defined [here](../type-layout.md#size-and-alignment), which also means that pointer pointing to memory that contains those values, needs to aligned correctly.

Because of this, each pointer type has an alignment associated with it, by default this is the alignment of the underlying type.
Alignment can be added decided manually by using the `align` pointer specifier.


Since alignment cannot be guaranteed to be at an address with greater alignment, pointer can only be implicitly cast to another pointer with the same alignment of less.
If the programmer can guarantee a pointer has a larger alignment, the core library contains use to cast a pointer with a lower alignment to one with a larger alignment.
If the value given does not adhere to the larger alignment, either an error value can be created, or [illegal behavior might occur](../../illegal-behavior.md#incorrect-pointer-alignment-)

> _Todo_: Specify which utilites

#### `allowzero` [↵](#alignment-)

`allowzero` is a special specifier that allows zero to be a valid pointer value.
This should not be confused with optional pointers, but is meant for cases, like an RTOS where the address is mappable.

Optional pointer with `allowzero` have a different size then those without `allowzero`.

#### Provenence [↵](#pointer-types)

Pointer provedence can be seen as some additional invisible data that is associated with a pointer, this can, for example, be information where a pointer comes from.
Given 2 pointer, there is no guarantee they are meant to be similar, even if they have the same value.

An example of this would be:
```
a := [1, 2, 3];
b := [4, 5, 6];

let p : [^]i32 = &a;
let q : [^]i32 = &b;

r := q + 3;

#assert(p == q);
```
Even though both `p` and `q` originally pointed to an element in 2 distinct sets of memory, the addition of 3 elements to `q` makes it overlap with `p` (as the stack grows downwards).
This can cause issues, as the compiler can see that `p` and `q` are supposed to point to 2 distinct array, so it can interpret this by assuming that the address of `p` and `q` can never overlap.

In an actual usecase, we can derive that they will overlap, as we have a compile time known offset, but if the `3` is a the result of a more complex runtime computation, we suddenly don't have this guarantee anymore (this is ignoring the fact we could actually insert a safety_check on the value we get out of the computation here).

> _Note_: This section is currently mainly informational, as provenence handling is still unresolved. An intersting writeup around this problem can be found [here](https://www.ralfj.de/blog/2022/04/11/provenance-exposed.html). Provence will be something that will be looked at later in the development cycle.
