# Bitfield types
```
<bitfield-type>     := [ 'mut' ] 'bitfield' [ <bitfield-backing-int> ] [ <bitfield-bit-order> ] '{' <bitfield-members> '}'
<bitfield-memebers> := [ <bitfield-fields> ] { <assoc-item> }*
```
A bitfield type is similar to a struct type, but which is tightly packed and allows each field to be offset and sized to a bit (non-byte) value.
A bitfield ignores the alignment of a type.

The bitfield type itself may have it's size explicitly defined, this is done by setting any [unsigned integer] as the underlying storage type.
If not specified, the compiler will use the minimum amount of bits needed to store all values within the type.

## Anonymous structs [↵](#struct-types)

An anonymous bitfield is what is produced when an explicit bitfield type is used.
These structs cannot be manually referenced, and therefore have a limited use.

Some usecases of an anonymous bitfield:
- to be assigned to type alias
- to be returned from a [meta function] that returns a type
- as a field of another type, allowing bitfield-like field access, but not have the type field explicitly usable
- as a nominal type of a local variable

> _Implementation note_: For better error reporting, the compiler will be default generate a name which includes relavent information to track it down.
>                        This behavior can be controlled explicitly in cases where this information should be restricted to only development.
> 
> The rules to decide the name are the following:
> 1. Fist a name is chosen based on the location of the bitfield:
>    - if used as the type of a variable, the name will be based on the name of the variable
>    - if used as a return type, the name will be based on the function it is returned by
>    - if produced by a meta function, the name will be based on the meta function
>    - if used inside of another composite type, the name will be based on the name of the composite type
>    - Otherwise, the name will be a generic name: `__anon_bitfield`
> 2. After this information about its span will be appended, which at minimum will contain the line and column of where the bitfield is defined.
>    In case this is defined within a meta function, the line and column will be taken from the location it is invoked
> 3. A pseudo-random value will be appened to the end to avoid any other name conflicts (this is not guaranteed to be stable across compilations).
>
> A compiler can enable truly random names for a bitfield, which will result in the bitfield getting a name of `__ab`, followed by a psuedo-random number.

## Fields [↵](#struct-types)
```
<bitfield-fields> := ( <bitfield-field> | <bitfield-padding> ) { ',' <bitfield-field> | <bitfield-padding> }* [ ',' ]
<bitfield-field>  := [ 'vis' ] [ 'mut' ] <name> ':' <type> [ <bitfield-field-size> ]
```

Fiels make up the data which is stored within the bitfield type.

Each field defines the name and type of the field.
Each field can also define its visibility, which controls in which location the field may be accessed.
In addition, a field may be made mutable, allow it to be modified after the structure is initially created, as by default each field may only be assigned when defining it in a [struct expression], and may not be modified later on.

### Field mutability [↵](#fields-) 

Each field in a bitfield is by default immutable, meaning it can only be set when the bitfield is initialized.
A field may be explicitly declared as mutable, allowing its value to be changed in other locations.

A bitfield type may also be defined as `mut`, this indicates that all fields within the bitfield will be mutable by default.
This has no effect on associated items.

> _Example_:
> ```
> mut struct {
>     a: i32,
>     b: u32,
> 
>     fn foo() {}
> }
> ```
> is equivalent to
> ```
> struct {
>     mut a: i32,
>     mut b: u32,
> 
>     fn foo() {}
> }
> ```

### Field visibility [↵](#fields-)

Each field may individually define its visibility.

This visibility defines the fields visibility relative to the location of the bitfield.

> _Note_: An anonymous bitfield do not support visibility on any field

### Padding [↵](#fields-)
```
<bitfield-padding> := '_' ':' <unsigned-type>
```

Padding can be provided using a field with a `_` name, and an unsigned integer type.

> _Example_
> ```
> bitfield Foo {
>     a: u3,
>     _: u4,
>     b: u1,
> }
> ```
> This bitfield will contain 4 bits of padding in between `a` and `b`

### Restrictions [↵](#fields-)

For a type to be able to be used, it must adhere to the following restrictions:
- must be `Copy`
- must be `Sized`
- must not implement `Drop`, and neither should any of its fields

Fields can not be borrow, nor can they have a pointer to them taken, as there is no guarantee a field lays on a byte boundary.

### Field sizes [↵](#fields-)
```
<bitfield-field-size> := ':' <expr>
```

Each field may also define the size it will take up within the bitfield.
This is done using a compile-time expression.

> _Example_
> ```
> bitfield Foo {
>     a: i32 : 2,
>     b: i32 : 4,
> }
> ```
> From a programmer perspective, both `a` and `b` will be an i32, but internally, `a` takes up 2 bits and `b` takes up `4` bits.

When variably sized types are used, they themselves may also encode an implicit size:
These types can be one of the following:
- [boolean types], excluding `bool`
- [integer types]
- other bitfield types

Additionally, if a range is provided, the field can be explicitly set to a certain bit offset.
By default, this offset must come after the end of the previous field, but this restriction can be remove using the `@bitfield_overlap` trait, allowing field to overlap.

_Example_
```
bitfield Foo {
    a: u2,
    // Fiels will be located at offset 4 in the bitfield and takes up 4 bytes
    b: u8 : 4..=7,
}
```

If a value is assigned, which cannot be represented by the number of bits provided for a value, it will result in  [illegal behavior].

> _Example_
> ```
> mut bitfield Foo {
>     a: i32: 2,
> }
> 
> // fine, all bits except for the first bit are set to 0
> f := Foo { a: 1 };
> 
> // illegal behavior: only the lower 2 bits may contain a non-0 value
> // f.a = 4;
> ```

## Backing integer [↵](#struct-types)
```
<bitfield-backing-int> := `as` <unsigned-type>
```

Each bitfield is an unsigned integer behind the scenes, and any value in the bitfield, are packed into this single integer.
This backing integer may be defined explicitly behind the bitfield.
If none is explicitly chosen, the bitfield will default to an unsigned integer with enough bits to contain all fields.
When it itself is not located within a bitfield, it will take up the next multiple of 8 number of bits, i.e. it takes up the remaining bits of the last byte with padding.

> _Example_:
> The following bitfield has a size of 20 bits:
> ``` 
> bitfield Foo {
>     a: u4,
>     b: u16,
> }
> ``` 
> When stored within another bitfield, this type will take in exactly 20 bytes, i.e. a `u20`.
> But outside of a bitfield, this will result in the bitfield taking up a `u24`, or an unsigned int that occupies 3 full bytes.

### Endianness [↵](#backing-integer-)

The endianness of the backing integer defines how the bytes of the bitfield will be laid out in memory.

This endianness has no impact on the internal layouts of each field within the bitfield.
A fields endianness is defined by how its bytes are laid out, once extracted from the bitfield, not when stored within the bitfield, as these represent the encoded layout of the value.

More info about the exact effect on the layout can be found in the [endianness section] of the bitfield layout.

### Bit order [↵](#struct-types)
```
<bitfield-bit-order> := 'in' ( 'msb' | 'lsb' )
```

The bit order defines how fields are laid out within the backing integer, specifically whether fields are laid out starting at the MSB (Most Significand Bit) or LSB (Least Significand Bit) of the backing integer.

More info about the exact effect on the layout can be found in the [bit order section] of the bitfield layout.

## Annonymous bitfield fields [↵](#struct-types)

Anonymous bitfield fields allow a bitfield to be directly defined within a different composite type, and allows its fields to be directly accessed from that type.

_Example_:
In a struct,  this can allow a set of packed values to be directly stored within the struct
```
struct Foo {
    bitfield {
        flag: bool,
        kind: u7
    },
    data: usize
}

f := Foo { flag: true, kind: 63, data: 3 };

// We can now directly access `Foo`'s `flag` and `kind` fields
kind := f.kind;
```

# Old

## Endianness [↵](#struct-types)

The endianness of bytes, is decided by the unsigned type provided, this only affects in which order the bytes of the bitfield is laid out.

Endianness may be applied to the bitfield or to an individual field.

Endianness is propagated the following way:
- if a field has an explicitly defined endianness, it will always use that endianness
- if a field has no endianness, it will take over the endianness of the bitfield it is in
  - if the bitfield does not have an endianess, it will:
    - take over the endianness of what ever bitfield contains it
    - if not in a bitfield, the compiler may choose an endianness

_Example_
```
bitfield Foo : u16le {
    a: u16
}

bitfield Bar : u16 {
    b: u16
}

bitfield Quux : u32be {
    f: Foo,
    b: Bar,
}
```
Will be converted to

```
bitfield Foo : u16le {
    a: u16 as u16le
}

bitfield Bar : u16 {
    b: u16
}

bitfield Quux : u32be {
    f: Foo as u16le,

    // b.b will be `as u16be`, as it takes over the same endianness of `Bar`
    b: Bar as u16be,
}
```

More info about the exact effect on the layout can be found in the [endianness section] of the bitfield layout.

## Bit layout [↵](#struct-types)
```
<bitfield-bit-order> := 'in' ( 'msb' | 'lsb' )
```
In addition to deciding the byte endianness, the order of bits may also be decided by specifying which byte order to use.

This order specifies how data is laid out inside of bytes, ignoring how bytes are laid out relative to each other.
It is used to define how to index into a given byte, meaning where to start and in which direction to index in.

The following layout represents the starting point to index from, and the direction to index in:
```
  7   6   5   4   3   2   1   0
+---+---+---+---+---+---+---+---+
|   |   |   |   |   |   |   |   |
+---+---+---+---+---+---+---+---+
 msb ->                   <- lsb
```

More info about the exact effect on the layout can be found in the [bit order section] of the bitfield layout.

> _Note_: Bit order on a bitfield only has an impact in how fields are laid out, values themselves are laid out as expected, meaning that their largest value will be in their relative MSB bit and their lowest in their relative LSB bit.

> _Warning_: If no explicit bit order is defined, the compiler may choose the optimal layout for the system. If the order of fields needs to be reliable, this should be defined explicitly.

## Record bitfield
```
<record-bitfield-type> := 'record' 'bitfield' [ <bitfield-backing-int> ] [ <bitfield-bit-order> ] '{' <bitfield-members> '}'
```
A record bitfield is a variant of a bitfield which is _structural_ instead of _nominal_.
These structs follow the rules defined [here](../nominal-vs-structural-types.md).

Some of the most notable ones for bitfields are:
- all fields are public
- all fields are mutable



[boolean types]:      ../builtin-types/boolean-types.md
[integer types]:      ../builtin-types/integer-types.md
[unsigned integer]:   ../builtin-types/integer-types.md#unsigned-integer-types-
[endianness section]: ../../type-layout/bitfield-layout.md#endianness-
[bit order section]:  ../../type-layout/bitfield-layout.md#bit-order-
[illegal behavior]:   ../../../illegal-behavior.md#bitfield-invalid-value
