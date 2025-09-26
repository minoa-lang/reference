# Self type
```
<self-type> := `Self`
```

The `Self` type is a special type which exists in multiple locations and evaluated to one of the following:
- In a [trait], it refers to the type that is implementing the trait
- In an [implementation], it refers to the type that is being implemented.
  When the type being implemented is a _composite type_, it can also be used to called the constructor or one of its initializers.
- In a definition of an [composite type], it refers to the type being defined.
  No fields within the [composite type] must directly use `Self` (i.e. requires indirection) to prevent an infinitely recursive type.

The `Self` type acts similar to a generic type parameter, as defined in the [`Self` scope] section.



[composite type]: ../composite-types.md
[trait]:          ../../items/traits.md
[implementation]: ../../items/implementations.md
[`Self` scope]:   ../../namespaces-scopes.md#self-scopes