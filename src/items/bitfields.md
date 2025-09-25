# Bitfields
```
<bitfield-item> := { <attribute> }* [ <vis> ] [ 'mut' | 'record' ] 'bitfield' <name> [ <generic-params> ] [ <bitfield-backing-int> ] [ <bitfield-bit-order> ] [ <where-clause> ] '{' <bitfield-members> '}'
```

A bitfield item defines a named bitfield type.
Ulike a plain [bitfield type], the cannot be anonymous.
This is similar of creating a distinct type alias to an anonymous bitfield, with some additional syntax features.

Similarly to a bitfield type, a `mut` specifier may be added to a bitfield type ,indicateing that all fields in the bitfield will be mutable.

In additon, bitfield items may also define the default visibility for all fields and associated items in the bitfield before the bitfield itself.

> _Example_
> ```
> bitfield Foo : u32le in msb {
>     a: u20,
>     b: u12,
> }
> ```

In addition, generic parameters and a where caluse can be added to the generated bitfield.

> _Example_
> ```
> bitfield Foo<T> : u32le in msb where T is Copy {
>     a: flag,
>     b: T,
> }
> ```



[bitfield type]: ../type-system/types/composite-types/bitfield-types.md