# String array & slice types
```
<string-kind> := 'str'
               | `str7`
               | `str8`
               | `str16`
               | `str32`
               | `cstr`
```

String array and slice types are special version of an array and slice respectively, which encode a string.
This means that the type comes with an additional guarantee, that the underlying data represents valid data for the kind of string that is defined.

These types are special variants of both an [array] and a [slice].
They are seperate types to allow additional guarantees to be provided via the compiler, as text handling is a very common operation.

Below is a table of the support string kinds:

Type    | Character type | Meaning        | Encoding
--------|----------------|----------------|----------------------------------------------
`str`   | `char`         | utf-8 string   | `u8`, containing only valid utf-8 sequences
`str7`  | `char7`        | utf-7 string   | `char7`
`str8`  | `char8`        | ANSI string    | `char8`
`str16` | `char16`       | utf-16 string  | `char16`, containing no unmatched surrogates
`str32` | `char32`       | utf-32 string  | `char32`
`cstr`  | `char8`        | C-style string | `char8`, null-terminated (0-sentinel)

With the exception of `str`, all data contained in the string will be a [valid characters] as defined by the character type.

If the underlying data does not match the above mentioned rules, it is considered [illegal behavior].

In addition, when using `str` or `str16`, creating a slice which is not located at a character boundary is [also illegal behavior].

## String array
```
<string-array-type> := <string-kind> '(' <expr> [ ':' <expr> ] ')'
```

Unlike an array, there is no guarantee that the array will contain a full string, only that it will be stored within a statically sized array with a given size.
Instead when there is a shorter string, it will terminate it with the sentinel value.
The sentinel for this will be `\0`, if no explicit one is defined.

If an explicit sentinel is provided, it will both add a sentinel at the end of the array, as expected for an array, and also use it to signal the end of the contained string.

## String slice

```
<string-slice-type> <string-kind> [ '(' ':' <expr> ')' ]
```

Similarly to any other [slice], as this type is backed by other data within the program, they are dynamically sized types and can therefore only be instantiated through a pointer or reference type.
In addition, it can also be provided with a sentinel.

Similarly, there is no guarantee that there is no guarantee that the sentinel does not appear anywhere else in the slice.



[array]:                 ./array-types.md
[slice]:                 ./slice-types.md
[valid characters]:      ../builtin-types/character-types.md
[illegal behavior]:      ../../../illegal-behavior.md#invalid-string-data-
[also illegal behavior]: ../../../illegal-behavior.md#string-slicing