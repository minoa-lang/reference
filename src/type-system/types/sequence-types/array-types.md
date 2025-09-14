# Array types
```
<array-type> := '[' <expr> [ ':' <expr> ] ']' <type>
              | <sentinel-terminated-array-type>
              | <enumerated-array-type>
              | <key-value-array-type>
```

An array type (`[N]T`) is a fixed-size sequence of `N` elements of type `T`
Array types are laid out as a contiguous chunk of memory.

An array's size expression needs to be a value that can be evaluated at compile time, and has a type of `usize`.
In location where the array is assigned an array expression, the size can be left out and replaced by a `_`, which will infer the size of the array.

_Example_
```
// Compiler infers the size of the array as 4 here
let a: [_]u8 = [ 0, 1, 2, 3 ];
```

Arrays support out-of-bound safety checks.

In addition, arrays may have certain operations applied to them at compile time, allowing the creation of new arrays with a combined size.
> _Example_
> ```
> let a: [4]u8 = [ 0, 1, 2, 3 ];
> let b: [4]u8 = [ 4, 5, 6, 7 ];
> 
> // This is equivalent to `let c: [8]u8 = [ 0, 1, 2, 3, 4, 5, 6, 7 ];
> let c = a ++ b;
> ```

## Multi-dimensional array [↵](#array-types)
```
<multi-dimensional-array-type> := '[' <expr> { ',' <expr> }+ ']' <type>
```

A multi-dimensional array is syntactic sugar for a set of nested arrays, i.e. `[2, 3, 4]u8` is equivalent to `[2][3][4]u8`.

In addition, a multi-dimensional array, when specified as above or as a list of nested array, support indexing using a [multi-value index].

> _Note_: When seeing a multi-dimensional array, an array like `[2, 3, 4]u8` is read as: An array containing 2 arrays, each containing 3 arrays of 4 u8's

> _Note_: If a multi-dimensional array needs to contain sentinels, it should be written as a nested array

> _Tooling_: Tooling is allowed to show either representation of a multi-dimensional array, while giving the user control of how this will be represented.
>            It is recommended to prefer the multi-dimensional array representation.

## Sentinel-terminated arrays [↵](#array-types)
```
<sentinel-terminated-array-type> := '[' <expr> ':' <expr> ']' <type>
```

An array can also have a sentinel value, which is declared after the size.
So an array `[N:M]T` has `N` elements of type `T`, with a sentinel value of `M`.
Like the size, the sentinel value needs to be evaluated at compile time, and has a type of `T`.

When a sentinel value is defined, the array will contain 1 additional element past its lenght, this is the sentinel value.
To assign an array, the [array expression] must contain `N` elements, and the sentinel value will be implicitly added, as it cannot be manually set.

The sentinal value may be retrieved by indexing the array using the array's length.
Writing to this value will result in an error.

There is no guarantee that a sentinel does not appear anywhere else in the array.

Sentinel values have a limited, but useful set of applications:
- interoperability with C and OS libraries which commonly expect a range of values to be terminated with a sentinel
- indicating the end of certain collections, possible avoiding additional checks based on the length of the data
- used as termination canaries

## Enumerated arrays [↵](#array-types)
```
<enumerated-array-type> := [ 'sparse' ] '[' <type> ']' <type>
```
In addition to `usize` sizes, a fieldless enum, generaly with an integer discriminant, may also be used as the size of an array, this is done by supplying the type of the enum as the size expression.
Enums without an integer discriminant are allowed, but are required to implement `EnumIndex`.

Enumerated arrays can be indexed using both an enum variant, or its corresponding discriminant value.

By default, the array will have the size of the largest discriminant value within it, + 1.

### Sparse arrays [↵](#enumerated-arrays-)

If the enum were to contain non-contiguous values, an enumerated enum may be prefixed using the `sparse` keyword.
This means that each enum value will be mapped to a corresponding index.
In this case, the array will have the size of the number of variants within the enum.

For an enumerated array to support the `sparse` attribute, the enum used to index the array must implement the `SparseIndex` trait.

> _Note_: Sparse arrays do come with a slight overhead for the translation of an the enum's value to the actual index.

> _Tooling_: It is recommended that tooling indicates whether an index into the array is sparse or not

## Key-value arrays [↵](#array-types)
```
<key-value-array-type> := '[' <expr> ']' '(' <type> ':' <type> ')'
```

A key-value array is a special variant of an array that allows key-value pairs to be stored.
While this might look similar to an array of tuples, the `:` is used to indicate that this maps keys to values.

Each element within a `[N](K:V)` is stored as a [`KeyValue(K,V)`]


[array expression]:  ../../../expressions/constructing-expressions.md#array-list-expression- "Todo: update once constructing expressions are split into multiple files"
[multi-value index]: ../../../expressions/index-expressions.md#multi-value-indices-
[`KeyValue(K,V)`]:   ../../../langauge-items.md#keyvalue- "Todo: In the future, make this refer to the correct documentation of this type"