# Slice types
```
<slice-type> := `[` `]` <type>
              | <sentinel-slice-type>
```

A slice type (`[]T`) is a variably sized type that represents a 'view' within a sequence of elements of type `T`.
Variably here means that the side of the slice cannot be changed directly, but only by taking any sub-slice of the slice.
This also means that a smaller slice cannot be extended to a larger slice.

A slice can be empty, meaning that is has a size of 0 and does not point to any values.

Since a slice is a view, it is always associated with a range of values that is owned by another item or local value, slices cannot exists on their own.
To prevent issues, they themselves are treated like unsized types, and with the exception of one at the end of some [composite types], they are generally used through reference or pointer-like types, for example:
- `&[]T`: a shared slice, often just called a slice. It borrows the data it point to.
- `&mut []T`: a mutable slice. It mutably borrows the data it point to.

> _Note_: No slice corresponding to an [enumerated array] exists.

## Multi-dimensional slice [↵](#slice-types)
```
<multi-dimensional-slice-type> := '[' { ',' }* ']' <type>
```

A multi-dimensional slice is syntactic sugar for a set of nested slices, i.e. `[,,]u8` is equivalent to `[][][]u8`.

In addition, a multi-dimensional slice, when specified as above or as a list of nested slices, support usign a [multi-value index].

> _Note_: if a multi-dimensional slice needs to containt sentinels, it should be written as a nested array

> _Tooling_: Tooling is allowed to show either representation of a multi-dimensional slice, while giving the user control of how this will be represented.
>            It is recommended to prefer the multi-dimensional array representation.

## Sentinel-terminated slices [↵](#slice-types)
```
<sentinel-slice-type> := '[' ':' <expr> ']' <type>
```

Like an array, a slice may also include a sentinel value, the slice will then contain 1 additional elements past its dynamically stored length, this is the sentinel value.
A sentinel-terminated slice can be created by using [sentinel indexing] on a supported type.

Since a slice is a view into an other data, there is no guuarantee that the sentinel does not occur anywhere else in code.

Sentinel values have a limited, but useful set of applications:
- interoperability with C and OS libraries which commonly expect a range of values to be terminated with a sentinel
- indicating the end of certain collections, possible avoiding additional checks based on the length of the data
- used as termination canaries

## Key-value slices [↵](#slice-types)
```
<key-value-slice-type> := '[' ']' '(' <type> ':' <type> ')'
```

A key-value slice is a special variant of an array that allows key-value pairs to be stored, similarly to its array equivalent.
While this might look similar to an slice of tuples, the `:` is used to indicate that this maps keys to values.

Each element within a `[N](K:V)` is stored as a [`KeyValue(K,V)`]



[enumerated array]:  ./array-types.md#enumerated-arrays-
[composite types]:   ../composite-types.md
[sentinel indexing]: ../../../expressions/index-expressions.md#sentinel-indexing-
[multi-value index]: ../../../expressions/index-expressions.md#multi-value-indices-
[`KeyValue(K,V)`]:   ../../../langauge-items.md#keyvalue- "Todo: In the future, make this refer to the correct documentation of this type"