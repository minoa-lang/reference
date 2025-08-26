# Bitfield types
```
<bitfield-type>    := [ 'mut' ] 'bitfield' [ '(' <expr> ')' ] '{' <bitfield-members> '}'
<bitfield-members> := <bitfield-field> { ',' <bitfield-field> } [ ',' ] { <assoc-item> }*
<bitfield-field>   := [ <vis> ] [ 'mut' ] <name> ':' <type> [ '|' <expr> ]
```

A bitfield type is similar to a struct type, but which is tightly packed and allows each field to be offset and sized to a bit (non-byte) value.
By default, a bitfield ignores the alignment of a type.

The bitfield type itself may have it's size explicitly defined, this is the number of bits the type would take up when it would be part of a bitfield.
Outside of anohter bitfield, the bitfield will have a size of the minimum amont of bits needed to store this size.
If the fields take in less bits than are specified, the remaining bits will be padding.
When the size of the fields take up more bits, the compiler will emit an error.

Each field in a bitfield is allowed to explicitly define how it should be packed.
This can be done by providing a compile time expression defining the size of the field.

Like struct fields, each bitfield field is allowed to specify their own visibility and mutability.

Each field within the tuple struct can have its visibility and mutability defined.
Each field can be accessed using a [tuple index expression](../../expressions/tuple-index-expressions.md), in addition, they may also have an optional name, which may be used to access a field via a [field access expression](../../expressions/field-access-expressions.md).

For consistency, bits are laid out in the following way:
- Bits go from MSB (most-significant bit) to LSB (least-significant bit)
- Field themselves follow the endianess of the type itself.

#### Record bitfield types [↵](#bitfield-types)
```
<record-bitfield-type> := 'record' 'bitfield' '{' <bitfield-memebers> '}'
```

A record bitfield, also known as a POD (Plain Old Data) bitfield, is a variant of a bitfield which is _structural_ instead of _nominal_.
These bitfields follow the rules defined [here](../nominal-vs-structural-types.md).

Some of the most notable ones for bitfields are:
- all fields are public
- all fields are mutable
