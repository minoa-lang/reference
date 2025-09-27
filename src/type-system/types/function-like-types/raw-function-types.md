# Raw function types
```
<raw-fn-type>         := [ 'unsafe' | 'safe' ] [ 'extern' [ '(' <abi> ')' ] ] 'fn' '(' <fn-type-params> ')' [ '->' <type-no-bounds> ]
<fn-type-params>      := <fn-type-param> { ',' <fn-type-param> } [ ',' ]
<fn-type-param>       := { <attribute> }* [ <fn-type-param-names> ':' ] <type>
                       | <fn-closure-param>
<fn-type-param-names> := <fn-type-param-name> { ',' <fn-type-param-name> }
<fn-type-param-name>  := <ext-name> | '_'
```

A raw function type is a special type which refers to a generalized version of a function with a given signature.
They are used to refer to a function whose identity is not known at compile-time.

Raw function types cannot exist by themselves and must be the sub-type of a [pointer-like type] to be used, which results in a so-called 'function pointer type'.
If behind a pointer type, the pointer type may not have a `volatile` attribute.
These function pointer can be created via the coercion of a function, or non-capturing closure, with a matching signature.

Raw function types can be marked as `unsafe` or `safe`, the latter is only useful when it is used to refer to a safe external function.
`unsafe` function types may be assigned 'safe' function, but not the other way around.
Any `unsafe` function type must be called from an unsafe context, even if the function assigned to it is safe.

Raw functions may also be specified as `extern`, with optionally an [ABI] it adheres to, this is the only location where the ABI is specified as part of an `extern` specifier.
If no explicit ABI is provided, the [default `minoa` ABI].
All external function will by default be marked as `unsafe`, and can be explicitly marked as 'safe'.

A pointer type's parameters may be provided by a name, this name is ignored when calling the function, but is useful for the following reasons:
- when provided with multiple names, the raw function type will have this parameter expanded into multiple parameters with the same type
- to allow the use of the names within documentation

> _Note_: A raw function type cannot be directly used by any code, it must **always** be behind a pointer.

> _Note_: The syntax for a 'raw function type' is similar to that of a [function closure parameter] or [function closure return].

> _Todo_: Would it make sense to support variadic parameters?


## Raw function representing trait types
```
<raw-closure-type> := [ 'mut' | 'move' ] [ 'async' ] 'fn' '(' <fn-type-params> ')' [ '->' <type-no-bounds> ]
```

In addition, a raw function type can also represent a trait type.
These can be used in location where both functions or closures could be used, but at limited to certain usecases.

The below table defines the desugared, where `Fn` can stand for any function-like trait.

Location                     | Desugared
-----------------------------|-----------
[function closure parameter] | `impl Fn`
[generic argument]           | `dyn Fn`

This table also defines in which locations the type may be used.

The specifiers applied to the type define which trait they will represent

Specifier    | Trait
-------------|----------
n/a          | `Fn`
`mut`        | `FnMut`
`move`       | `FnOnce`
`async`      | TBD
`mut async`  | TBD
`move async` | TBD




[pointer-like type]:          ../pointer-like-types.md
[trait types]:                ../trait-types.md
[ABI]:                        ../../../abi.md
[default `minoa` ABI]:        ../../../abi.md "Todo: fix section"
[function closure parameter]: ../../../items/functions.md "Todo: fix section"
[function closure return]:    ../../../items/functions.md "Todo: fix section"
[generic argument]:           ../../../generics.md#generic-arguments-