# String slice type
```
<string-slice-type> := 'str' | 'str7' | 'str8' | 'str16' | 'str32' | 'cstr'
```

A string slice typre repesents a special slice, encoding a string.
This is a separate type, which allows string specific functionality.
In addition, they assure that the underlying data is valid data for their representive encoding type.

Calling a string slice method on invalid underlying data is [illegal behavior].

String slices, like regular slices, are dynamically sized types and can therefore only be instantiated through a pointer or reference type.

Below is a table of all string slice types

Type    | character type | internal representation | Meaning
--------|----------------|-------------------------|-----------------------------------------
`str`   | `char`         | `[]u8`                  | utf-8 string
`str7`  | `char7`        | `[]char7`               | utf-7 string
`str8`  | `char8`        | `[]char8`               | ANSI string
`str16` | `char16`       | `[]char16`              | utf-16 string
`str32` | `char32`       | `[]char32`              | utf-32 string
`cstr`  | `char8`        | `[;0]char8`             | C-style string, includes null-terminator



[illegal behavior]: ../../illegal-behavior.md