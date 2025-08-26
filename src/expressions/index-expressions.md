# Index expressions
```
<index-expr> := <expr> [ '?' ] '[' [ '^' ] [ '?' ] <expr> { ',' <expr> }* [ ',' ] ']'
```

An index expression can be used to get a value out of a type using a given index.

An index expression may also indicate that it uses reverse indexing, meaning that the index is an offset from the end by using the `^` character.

In addition to direct indexing, there is also a variant allowing for the index expression to return an optional value that will be `.None` when no value is found at the given index.
This is done by using the `[?expr]` version of the indexing expression.

Similar to a [field access expression](./field-access-expressions.md), tuple index expressions also support `?.` (null-propagating) accesses.
The indexing will only happen if the value it is called on is a non 'err' value, otherwise it will propagate this value.
The `?.` access is supported on any type that implements the `OptAccess` trait.

When the expression being indexed is either an array or a slice, it will get the relevant element at a given index or a subslice at the given range.
If the array of slice is mutable, the resulting value will be memory location that can be assigned to.

Indices are 0-based for arrays and slices.
If array access is a constant expression and the array has a known size, bounds can be checked at compile-time, otherwise, if the relevant safety check is enabled, the check will be performed at runtime and will panic when being indexed out of range.

For any other type, the resulting indexing will depend on whether the index implementation returns a reference or not.

Multiple index expression can be provided, these will implicitly get converted to a tuple expression when calling the relavent indexing method.
When a multi-dimenional array or slice (including mixed arrays and slices) is used, the comma separated list can be used to index into all of them using a single expression, meaning `a[b][c]` can be written as `a[b,c]`.

For all other types, the following operations will happen:
- In an immutable place context, the resulting value will be `Index::index(&a, b)` or `OptIndex::opt_index(&a, b)`.

  If the index implementation were to return a reference, it will be implicitly dereferenced, this can be avoided by taking a reference to the returned element.

- In a mutable place context, the resulting value will be `*IndexMut::index_mut(&a, b)` or `OptIndexMut::opt_index_mut(&a, b)`.

Indexing allows comma expressions to be passed, this will implicitly be converted to an index call taking in a tuple of expressions.

The interfaces associated with the index expressions are:
- `Index`
- `IndexMut`
- `OptIndex`
- `OptIndexMut`
- `RevIndex`
- `RevIndexMut`
- `RevOptIndex`
- `RevOptIndexMut`

> _Todo_: Can we unify this a bit more?
