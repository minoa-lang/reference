# Template string expressions
```
<template-string-epxr> := ( '$' | <name> ) <string-literal>
```

A template string expressions can be seen as a special function call to a template string function, but works by directly prepending the name of it in front of the string literal.
Template string allow for interpolations to be added in the string passed to them.

In addition to interpolation, it is also common practice that in cases where the template string function is called like a normal function, to allow value to be passed as positionals, using `{N}`, where `N` represents the index within the variadic arguments passed to said function.
Although this practice may differ between different functions.

A special template string exists which is prefixed by `$`, this calls the format string template as defined in the implicit context, allowing for a convenient shortcut to format a string.

The functions that are used for template string functions are defined [here](../items/functions.md#string-template-functions-)
