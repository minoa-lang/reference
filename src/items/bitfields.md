# Bitfields
```
<bitfield-item> := { <attribute> }*  [ <vis> ] [ 'record' | 'mut' ] 'bitfield' <name> [ <generic-params> ] [ ':' <expr> ] [ <where-clause> ] '{' <bitfield-members> '}'
```

A bitfield item is syntactic sugar to more easily define a named [bitfield type](../type-system/types/bitfield-types.md).

A bitfield's visibility defines only the visibility of the bitfield, and not any of its fields.

Meanwhile, a bitfield's mutability will be propagated as the mutability of the bitfield.

An enum declaration without any generic arguments, like:
```
bitfield name {
    // ...
}
```
Is equal to the following:
```
type name = bitfield {
    // ...
};
```

> _Todo_: Generics
