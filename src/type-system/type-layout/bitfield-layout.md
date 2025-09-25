# Bitfield layout

The layout of a bitfield is independent on any [layout representation] and will always follow the layout as defined in this section.

Fields are laid out in the order as they are defined within the bitfield's backing integer value.

The layout is define by 2 properties:
- the endianness
- the bit order

## Endianness [↵](#bitfield-layout)

Endianness determines how the data in the bitfield will be laid out.
Since a bitfield is backed with an integer value, this integer itself will have a endianness.

Thise solely encodes the order of the bytes used to read the backing integer from memory.

> _Example_
> Assuming a bitfield, whose values once packed represent the integer value `0x0123456789ABCDEF`.
> 
> The endianness of the integer will then define this layout in memory either, depending endiannes, as:
> - little endian:
>   ```
>     0  1  2  3  4  5  6  7
>   +--+--+--+--+--+--+--+--+
>   |EF|CD|AB|89|67|45|23|01|
>   +--+--+--+--+--+--+--+--+
>   ```
> - big endian:
>   ```
>     0  1  2  3  4  5  6  7
>   +--+--+--+--+--+--+--+--+
>   |01|23|45|67|89|AB|CD|EF|
>   +--+--+--+--+--+--+--+--+
>   ```


## Bit order [↵](#bitfield-layout)


The bit order defines how individual bits are laid out within the backing integer.
Specifically in which direction bits are indexed and fiels are laid out..

The below diagram shows the starting bit of each mode, and in which direction the index moves.
```
  7   6   5   4   3   2   1   0
+---+---+---+---+---+---+---+---+
|   |   |   |   |   |   |   |   |
+---+---+---+---+---+---+---+---+
 msb ->                   <- lsb
```

> _Example_
> The following bitfield
> ```
> bitfield Foo: u32 {
>     a: u4,
>     b: u5,
>     c: u11,
>     d: u12,
> }
> ```
> Will have its fields laid out in the backing integer depending on the bit order as:
> - lsb (least significand bit):
```
      3                                       2                                       1                                       0
  1   0   9   8   7   6   5   4   3   2   1   0   9   8   7   6   5   4   3   2   1   0   9   8   7   6   5   4   3   2   1   0
+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+
| D | D | D | D | D | D | D | D | D | D | D | D | C | C | C | C | C | C | C | C | C | C | C | B | B | B | B | B | A | A | A | A |
+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+
```
> - msb (most significand bit)
 ```
      3                                       2                                       1                                       0
  1   0   9   8   7   6   5   4   3   2   1   0   9   8   7   6   5   4   3   2   1   0   9   8   7   6   5   4   3   2   1   0
+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+
| A | A | A | A | B | B | B | B | B | C | C | C | C | C | C | C | C | C | C | C | D | D | D | D | D | D | D | D | D | D | D | D |
+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+
 ```

> _Note_: Bit order is unrelated to how data inside of each field is laid out, only in which order multiple fields are laid out.
>         This means that a value of 4 will always be represented as `0100`, and **never** as `0010`.

> _Note_: If data in the fields is needed in an `msb` bit order, it should be parsed manually

## Bitfield encoding example [↵](#bitfield-layout)

To provide a good overview of how a bitfield gets encoded, this example gives a step-by-step example of this process

Assuming we have the following bitfield:
```
bitfield Foo: u32le in lsb {
    a: u6,
    b: u4,
    c: u2,
    d: i32: 20
}
```
and we want to create the following value:
```
f: Foo {
    a: 45,
    b: 5,
    c: 3,
    d: 421337
}
```

1) We first need to convert each value to it's bit representation, containing the correct number of bits for the bitfieldd
```
a: 45     => 0b10_1101
b: 5      => 0b0101
c: 3      => 0b11
d: 421337 => 0b0110_0110_1101_1101_1001
```

2) We will calculate the offset for each field.

This is done by taking the offset of the current field, and adding the size of the current field to it
```
a:           0
b: 0 + 6  => 6
c: 6 + 4  => 10
d: 10 + 2 => 12
```
3) Then we will shift each value to be in the correct location relative to the bit order.

Since we are using `lsb` order, we shift each value left by it's offset, as its last bit needs to be at the bit at the offset
(x represents the placeholders in the shifted bits)
```
a: << 0  => 10 1101
b: << 6  => 01 01xx xxxx
c: << 10 => 11xx xxxx xxxx
d: << 12 => 0110 0110 1101 1101 1001 xxxx xxxx xxxx
```
4) Combine all fiels into the resulting integer
```
xxxx xxxx xxxx xxxx xxxx xxxx xx10 1101
xxxx xxxx xxxx xxxx xxxx xx01 01xx xxxx
xxxx xxxx xxxx xxxx xxxx 11xx xxxx xxxx
0110 0110 1101 1101 1001 xxxx xxxx xxxx
---------------------------------------
0110 0110 1101 1101 1001 1101 0110 1101

or ox66DD9D6D
```

5) Finally split the integer in bytes and store the encoded intenger in memory as defined by irs endianness
```
+--+--+--+--+
|6D|9D|DD|66|
+--+--+--+--+
or 
[ 0x69, 0x9D, 0xDD, 0x66 ]
```


[layout representation]: ./layout-representation.md