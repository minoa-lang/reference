# Index expressions
```
<index-expr> := <expr> [ '?' ] '[' [ '?' ] <expr> [ ':' <expr> ] ']'
<index>      := [ <name> ':' ] <expr> [ ';' <expr> ]
```

An index expression can be used to get an element or a slice of elements from a type at the given index.

A version of the index expressions which can safely handle invalid indices is also available
This version can be used by preceeding the index exprssion with a `?`.
This will commonly return one of the following types:
- an [optional type], where `null` indicates an invalid indexing operation
- a [result type], where `.Err(...)` indicates an invalid indexing operation

Within an index expression, the [end-ralative operator] is available, allowing indexing relative the the end of the index.
This operator is not guaranteed to be available and depends on the implementation of its respective index trait.


The following set of traits are associated with indexing expressions:

expression                        | trait
----------------------------------|---------------
`expr[index]` or `&expr[index]`   | `Index`
`&mut expr[index]`                | `IndexMut`
`expr[?index]` or `&expr[?index]` | `TryIndex`
`&mut expr[?index]`               | `TryIndexMut`

With the exception of arrays and slices, the following operation will happen when calling the expression `a[b]`:
- In a value or immutable place context, the resulting value will be `Index::index(&a, b)` or `TryIndex::try_index(&a, b)`
- In an mutable place context, the resulting value will be `*IndexMut::index_mut(&mut a, b)` or `*TryIndexMut::try_index_mut(&mut a, b)`

Whenever an an index implementation were to return a reference, it will be implicitld dereferences, unless when explicitly borrowing the returned value.
This will not re-borrow the value, but will ensure the dereference does not happen.


> _Example_
> ```
> arr: [10]i32 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
> 
> // regular index
> assert(arr[2] == 3);
> assert(&arr[4..6] == &[5, 6]);
> 
> // index relative to the end of the array, i.e. index at arr.len() - idx
> assert(arr[^3] == 7);
> assert(&arr[4..^3] == &[5, 6])
> 
> // Index outside of range with an optional indexing operation
> assert(arr[?11] == null);
> assert(arr[9..11] == null);
> ```

## Labelled indexing [↵](#index-expressions)

Each index may have a label defined before it, in case the implementation expects a labelled index.
The labels are provided as part of the indexing tuple type, meaning to have a label, the indexing type must be at least a [1-ary named tuple].

This also allows for multiple index implementations with the same type, but different labels.

> _Example_
> ```
> struct Foo {
>     // Index without label
>     impl as Index[i32] -> ... { // (0)
>         // ...
>     }
> 
>     impl as Index[(key: i32,)] { // (1)
>         // ...
>         fn(&self) index(index: i32) -> ... { ... }
>     }
> }
> 
> foo := Foo;
> 
> // calls (0)
> val := foo[10];
> 
> // calls (1)
> val := foo[key: 10];
> ```

## Optional chaining [↵](#index-expressions)

Similar to an [optional field access], the index expression support optional chaining, also known as null-propagating indexing.
This means that the indexing operation only happens when the left-hand operand is a value that does not have an erronous value.

This operation is handled by the `OptAccess` trait and works on all type implementing it.

> _Example_
> ```
> a := ?[4]i32 = [1, 2, 3, 4];
> b := ?[4]i32 = null;
> 
> assert(a?[2] == 3);
> 
> // the `null` value is propagated and the indexing is skipped
> assert(b?[2] == null);
> ```

## Multi-value indexing [↵](#index-expressions)

An index operation is allowed to take in a comma expression of values to be used as the index.
In cases like this, the implementation will be given a tuple of indices.

> _Example_
> ```
> a: IndexableType = ...;
> 
> b := a[0, 4];
> ```

In addition, multi-value indexing also supports labelled indexing.
This can be done by passing a string for each element of the tuple defined as an indexing type.

> _Example_
> ```
> struct Foo {
>     impl as Index[(x: i32, y: i32)] {
>         // ...
>     }
> }
> 
> foo := Foo;
> 
> ```

## Sentinel indexing [↵](#index-expressions)

Indexing operation may also provide an expected sentinel value.
This value is an optional value that is provided to each indexing call and is mainly used to check if the indexing operation resulting in a value which is followed by a sentinel.

Sentinel indexing is the only way, other than from a literal, to obtain a sentinel-terminated slice.

> _Example_
> 
> A builtin example of this would be when trying to create a sentinel-terminate slice from an array of element.
> It checks if the resulting slice is followed by a corresponding sentinel value, and that this sentinel value does not go out of range of the 
> ```
> arr: [5:0]i32 = [1, 2, 0, 3, 4];
> 
> // takes the slice or a range, and check if the following byte is a) in range, and b) has a valid sentinel
> a := arr[0..2;0];
> b := arr[3..5;0];
> 
> // Failing to have a correct sentinel value, will panic
> // c := arr[0..3;0];
> ```

Sentinel indexing is syntactic sugar for
```
a := arr[0..2, sentinel: 0];
```

Meaning that any implementation that takes in a tuple of which the last element is a named field with the name `sentinel`, adds support for sentinel indexing
```
fn Foo {
    impl as Index[(i32, sentinel: i32)] {
        // ...
    }
}
```

## Array and slice indexing [↵](#index-expressions)

Since indexing into an array or slice has some special handling, these behaviors are defined in this section.

When the expression being indexed is iether an array or a slice, ti will get the relavent element at a given index or sub-slice at the given range.

Indices for array and slice indexing are 0-based.
If the indexing is a contant exprssion, and it is done on an array with a known size, the bounds will be checked at compile time and will return any errors at this time.
If this is not possible, the bounds checking will be done at runtime.

When the indexing happens at set of nested arrays (`[N][M]T`) or slices(`&[]&[]T`), the index supports multi-value indexing, where each index supplied is the index for the array or slice that position.
The actual index operation will be decomposed into a set of individual indices, meaning that `expr[n, m]` will be decomposed to `expr[n][m]`

> _Example_
> ```
> arr := [
>     [1, 2, 3],
>     [4, 5, 6],
>     [7, 8, 9]
> ];
> 
> assert(arr[2, 1] == 8);
> ```


[optional field access]: ./field-access-expressions.md#optional-chaining- "Todo: section does not exists yet"
[end-ralative operator]: ../operators/special-operators.md#end-relative-operator-
[optional type]:         ../type-system/types/abstract-types/optional-types.md
[1-ary named tuple]:    ../type-system/types/composite-types/tuple-types.md#named-tuples-
[result type]:           ../type-system/types/abstract-types/result