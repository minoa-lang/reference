# Slice types
```
<slice-type> := `[` [ ';' <expr> ] `]` <type>
```

A slice type (`[]T`) is a dynamically sized type that represents a 'view' within a sequence of elements of type `T`.

A slice can be empty, meaning that is has a size of 0 and does not point to any values.

Slices are generally used through reference or pointer-like types:
- `&[]T`: a shared slice, often just called a slice. It borrows the data it point to.
- `&mut []T`: a mutable slice. It mutably borrows the data it point to.

#### Sentinel-terminated slices [↵](#slice-types)

Like an array, a slice may also include a sentinel value, the slice will then contain 1 additional elements past its dynamically stored length, this is the sentinel value.
This value is meant to prevent accidental out of bounds writes.

Sentinel value mainly exist for interoperability with C and OS libraries that commonly expect a range of values ending in a sentinal value,
but these are not that useful when writing Minoa code itself

See the [index expression] for more info about how to create a sentinal terminated array.



[index expression]: ../../expressions/index-expressions.md