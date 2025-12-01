# Template string expressions
```
<template-string-epxr> := ( '$' | <name> ) <string-literal>
```

A template string expression allows a [template string function] to be called, by prepending its name in front of a [string literal], most commonly [interpolated strings].
This expression is the only way to call template string expressions.

Additionally, a special template string exists, which is prefixed using a `$`, this indicates a formatted string, allowing for a convenient shortcut to format a string.

> _Todo_: How this format string can be overwritten by a user has not yet been determined.

```
template fn sql(input: InterpStringLiteral) -> SqlQuery { ... }

table := "data";
query := sql"SELECT * FROM \{table}";

template fn name(input: InterStringLiteral, first_name: &str, last_name: &str) -> Name { ... } 

first_name := "John";
last_name := "Doe";
name := name"\{first_name} \{last_name}";

// error: not enough interpolated values
// name := name"\{first_name} Doe";

middle_name := "Jane";
// error: too many interpolated values
// name := name"\{first_name} \{middle_name} \{middle_name}";

// error: interpolate values don't match the expected types
// name := name"\{first_name} \{10}";
```

[string literal]:           ../literals.md#string-literals-
[interpolated strings]:     ../literals.md#string-interpolation-
[template string function]: ../items/functions.md#template-string-functions-