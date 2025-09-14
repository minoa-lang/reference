# Inferred types
```
<inferred-type> := '_'
```

An inferred type tell the compiler to infer the type (if possible) based on the surrounding information available.
Inferred types cannot be used in generic arguments.

Inferred types are often used to let the compiler infer the type of generic parameters:
```
let a : DynArr(_) = ...;
```