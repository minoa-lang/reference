# Opaque types
```
<opaque-type> := 'opaque' [ '(' <expr> ')' ]
```

Any opaque type represents an possibly unknown, but non-zero sized type.
The size of the opaque type may also be explicitly sized by passing a compile time constant to it.

> _Note_: These types can be compared to the following:
> - when sized as `[N]u8`, where `N` is the size of the opaque type
> - when unsized as `dyn ?Sized`

A sized opaque type allows for it to be part of a sized compount type, without that compound type being a dynamically sized type.
An unsized opaque type needs to be behind a pointer type.