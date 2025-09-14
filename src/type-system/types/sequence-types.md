# Sequence types
```
<sequence-types> := <array-type>
                  | <slice-type>
                  | <string-slice-type>
```

A sequence type is a type that represents a sequence of subtypes, which are laid our linearly in memory.
These types cannot have their size changed at runtime.

These types can be statically or variably sized.
Variably sized means that the size is only known at runtime.