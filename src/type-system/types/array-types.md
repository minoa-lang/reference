# Array types
```
<array-type> := [ 'sparse' ] '[' <expr> [ ';' <expr> ] ']' <type>
              | <key-value-array>
```

An array type (`[N]T`) is a fixed-size sequence of `N` elements of type `T`
Array types are laid out as a contiguous chunk of memory.

An array's size expression needs to be a value that can be evaluated at compile time, and has a type of `usize`.

Arrays support out-of-bound safety checks.

> _Todo_: Enumerated arrays, i.e. [Enum]T

#### Sentinel-terminated arrays [↵](#array-types)

An array can also have a sentinel value, which is declared after the size.
So an array `[N;M]T` has `N` elements of type `T`, with a sentinel value of `M`.
Like the size, the sentinel value needs to be evaluated at compile time, and has a type of `T`.

When a sentinel value is defined, the array will contain 1 additional element past its lenght, this is the sentinel value.

Sentinel value mainly exist for interoperability with C and OS libraries that commonly expect a range of values ending in a sentinal value,
but these are not that useful when writing Minoa code itself

#### Enumerated arrays [↵](#array-types)

In addition to `usize` sizes, a fieldless enum may also be used as the size of an array, this is done by supplying the type of the enum as the size expression.
This allows the array to be directly indexed using an enum's discriminant value.

By default, the array will have the size of the largest discriminant value within it, + 1.

If the enum were to contain non-contiguous values, an enumerated enum may be prefixed using the `sparse` keyword.
This means that each enum value will be mapped to a corresponding index.
In this case, the array will have the size of the number of variants within the enum.

> _Note_: Sparse arrays do come with a slight overhead for the translation of an the enum's value to the actual index.

#### Key-value arrays [↵](#array-types)
```
<key-value-array> := '[' ':' <type> ']' <type>
```

A key-value array is a special variant of an array which stores key-value pairs.
