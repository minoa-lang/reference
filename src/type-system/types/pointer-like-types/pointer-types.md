# Pointer types
```
<pointer-type>   := <single-element-pointer>
                  | <multi-element-pointer>
```

A pointer type, also called a "raw pointer", represents an address in memory containing the underlying type.

Pointer types provide no guarantee about the underlying data, and may therefore point to a location storing different information, or to value that no onger exists.
For this reason, while not discouraged, it is recommended to prefer [reference types] wherever possible.

Additionally, even when a [reference type] is not applicable in a location, it is still recommended to use a so-called "smart pointer", as they can provide additional safety guarantees.

Some of the main usecases for raw pointers would be the following:
- interoperability with external libraries
- memory-mapped I/O (via [volatile pointers])
- low-level or performance critical code that does not allow [references] or smart pointers

Copying or dropping pointer has no effect on the lifecycle of any value.
Derefencing a pointer is an `unsafe` operation.

When comparing pointers, they are compared by their address, rather than what they point to.
And when done on pointers to dynamically sized types, they also have their additional metadata compared.

A pointer can only contain a 'null' value when:
- wrapped in an optional type
- having a [`allowzero`] specifier

> _Todo_: Should pointers handle ownership, some language item, or should it be something API based? Probably a language item, so give additional info to the compiler
 
## Single element pointers [↵](#pointer-types)
```
<single-element-pointer> := '^' [ <ptr-specifiers> ] <type>
```

A single element pointer `^T` or `^mut T`, refers to exactly 1 element in the memory pointed to.

This pointer can be converted to a reference by re-borrowing it using `&^` or `&mut ^`.

Single element pointers do not allow any arithmetic between pointer and integers, and therefore only support subtracting pointer from eachother, i.e. `ptr - ptr`.
Single element pointers are allowed to be sliced to a single element slice.

> _Example_: The pointer `^i32` would represent a pointer to a single immutable `i32` value.

## Multi element pointers [↵](#pointer-types)
```
<multi-element-pointer> := '[' '^' [ ':' <expr> ] ']' [ <pointer-specifier> ] <type>
```

A multi-element pointer `[^]T` or `[^]mut T`, pointing to an unknown number of elements.

In addition to the operations allowed on a single element pointer, a multi-element pointer allows additional functionality:
- pointer and integer arithmetic
- slicing with any range
- indexing of the underlying memory

Multi-element pointers require a type with an known size and alignment, meaning that multi-element pointer of dynamically sized types are not allowed.

Unlike slices, mutli-element pointers do not support safety checks.

> _Example_: The pointer `[^]i32` would represent a pointer to an unknown number of immutable `i32` values.

### Sentinel-terminated pointers [↵](#multi-element-pointers-)

Similarly to [arrays] and [slices], a multi-element pointer can have a sentinel.
This results in a pointer which allows access to all succeeding elements, until the first encounter of the sentinel value.

The main purpose of this type is to prevent buffer overflows when interacting with C-style and OS code.

## Pointer specifiers
```
<ptr-specifiers> := [ <ptr-align> ] [ 'allowzero' ] [ 'volatile' ] [ 'mut' ]
```

Pointers can have additional specifiers added to them, which change the behavior of the pointer.

### Alignment [↵](#pointer-types)
```
<ptr-align> := 'align' '(' <expr> ')'
```

Each pointer has an alignment, which by default is defined by the [alignment] of the type it contains.
This requires that any memory location stored within the pointer adheres to this alignment, otherwise it results in a [misaligned pointer], which is illegal behavior.

The `align` specifier allows this aligment to be manually declared.
The alignment needs to follow the following rules:
- must be a power of 2
- must be less than or equal to 2^20

As mentioned above, a pointer without an explicit alignment is equivalent to `^align(align_of(T)) T`.

Since alighment cannot be guaranteed to be at an adress with an equal or greater aligment, it is not possible to implicilty a pointer to another pointer with a greater alignment.
Although this can be done with any explicit cast, if this is an unfallible cast, a safety check will be inserted.

### Volatile pointers [↵](#pointer-types)

Pointers operations are generally assumed not to have any side-effects, and the optimizer uses that fact to be able to optimize the code.
An issue occurse when the pointer points to memory that does have side effects, like MMIO (Memory Mapped I/O).

To handle this correctly, `volatile` pointers can be used, they allow side effects.
They are also guaranteed to not be re-ordered relative to each other, i.e. any read from or write to them, will happen in the same order as defined in code.

> _Note_: `volatile` pointers are unrelated to atomics, in usecases where `volatile` is used with atomics, this is most likely in error.

### `allowzero` [↵](#alignment-)

The `allowzero` specifier allows a pointer to be assigned a 'null' value.

> _Warning_: This should only be used in cases where a value of `0` is a valid and mappable address, such as in an OS.
>            Otherwise, an optional pointer, meaning a pointer wrapped in an optional type, should be used.




`allowzero` is a special specifier that allows zero to be a valid pointer value.
This should not be confused with optional pointers, but is meant for cases, like an RTOS where the address is mappable.

Optional pointer with `allowzero` have a different size then those without `allowzero`.

## Optional pointers [↵](#pointer-types)

An optional pointer is a pointer wrapped inside of an [optional type], i.e. `?^T` or `?[^]T`.
These pointers are allowed to have a 'null' value, represented as `.None`.

The are guaranteed to have the same size as a pointer, thanks to optional type optimizations.

> _Note_: Optional pointers with `allowzero` have a different size then those without `allowzero`, as they are not allowed to assume that 'null' is an invalid value.

## Provenence [↵](#pointer-types)

Pointer provedence can be seen as some additional invisible data that is associated with a pointer, this can, for example, be information where a pointer comes from.
Given 2 pointer, there is no guarantee they are meant to be similar, even if they have the same value.

> _Example_
> ```
> a := [1, 2, 3];
> b := [4, 5, 6];
> 
> let p : [^]i32 = &a;
> let q : [^]i32 = &b;
> 
> r := q + 3;
> 
> assert(p == q);
> ```

Even though both `p` and `q` originally pointed to an element in 2 distinct sets of memory, the addition of 3 elements to `q` makes it overlap with `p` (as the stack grows downwards).
This can cause issues, as the compiler can see that `p` and `q` are supposed to point to 2 distinct array, so it can interpret this by assuming that the address of `p` and `q` can never overlap.

In an actual usecase, we can derive that they will overlap, as we have a compile time known offset, but if the `3` is a the result of a more complex runtime computation, we suddenly don't have this guarantee anymore (this is ignoring the fact we could actually insert a safety_check on the value we get out of the computation here).

> _Note_: This section is currently mainly informational, as provenence handling is still unresolved. An intersting writeup around this problem can be found [here](https://www.ralfj.de/blog/2022/04/11/provenance-exposed.html). Provence will be something that will be looked at later in the development cycle.




[volatile pointers]:  #volatile-pointers-
[`allowzero`]:        #allowzero-
[references]:         ./reference-types.md
[reference type]:     ./reference-types.md
[reference types]:    ./reference-types.md
[arrays]:             ../sequence-types/array-types.md
[slices]:             ../sequence-types/slice-types.md
[optional type]:      ../abstract-types/optional-types.md
[alignment]:          ../../type-layout.md#size-and-alignment
[infallible cast]:    ../../../expressions/type-cast-expressions.md "Todo: Fallibility section"
[misaligned pointer]: ../../../illegal-behavior.md#incorrect-pointer-alignment-