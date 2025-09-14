# Pointer-like types
```
<pointer-like-types> := <pointer-type>
                      | <reference-type>
```

A pointer-like type is any type which refers to data stored at a location in memory, specifically, it stores an address to the data.

Transmuting pointer-like types are susceptible to 

## Bit validity

Even though pointers and references are similar to a [`uptr`], as they have the same size and can have similar generated machine code.
The semantics of transmuting these 



[illegal behavior]: ../../illegal-behavior.md#pointer-transmute-
[`uptr`]: ./builtin-types/integer-types.md