# Opaque types
```
<opaque-type> := 'opaque' [ '(' <expr> ')' ]
```

An opaque type represents a type with an unknown layout, which can either be dynamically or statically sized.
If no size is given, the opaque time has an unknown, but non-zero size.
If a size is set, the size must be given by a compile time evaluated expression.

> _Note_: Internally, opaque types are represented:
> - when sized as `[N]u8`, where `N` is the size of the opaque type
> - when unsized as `dyn ?Sized`

A sized opaque type should be prefered when the size it would take up is known and its use location requires to be sized.
Otherwise an unsized opaque type should be used.
