# Function pointer types
```
<fn-type> := [ 'unsafe' [ 'extern' <abi> ] ] 'fn' '(' <fn-type-params> ')' [ '->' <type-no-bounds> ]
<fn-type-params> := <fn-type-param> { ',' <fn-type-param> }* [ ',' ]
<fn-type-param> := { <attribute> }* [ ( <name> | '_' ) { ',' ( <name> | '_' ) }* ':' ] <type>
```

A function pointer type can refer to a function whose identity is not known at compile time.
The can be created via coercion from functions and non-capturing closures with a matching signature.

If a function is marked `unsafe`, it is able to be assgined from both safe and unsafe functions, and must be called from an unsafe context.
To assign a pointer with a specific ABI, the function needs to be marked as an `extern` function with a matching ABI.
If not marked with a ABI, it will default to the `"C"` ABI.

Parameters may contain one or more names, but for the purposes of a function pointer these names are ignored, but are instead useful for documentation.
If multiple names are are given for a single parameter, these will be split into multiple parameters with the same type, matching the amount of names supplied.

With the exception as types within a parameter list, function pointers cannot be stored or passed as their own type, but instead need to be behind either a pointer or reference.
When used in in a parameter list, they represent a closure type, this is then known as a closure param type.

> _Note_: Whenever the term 'raw function pointer type' is used, it represent a free-standing pointer type that is not part of a another type

> _Todo_: Variadic parameters

