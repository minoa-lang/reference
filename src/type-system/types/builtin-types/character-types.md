# character types
```
<character-type> := 'char' | 'char7' | 'char8' | 'char16' | 'char32'
```

A character type is primitive type that can represent unicode characters.

Below is a table of supported character types:

Type     | Meaning           | Bit-width | Bit-width in bitfield | Valid range
---------|-------------------|-----------|-----------------------|------------------------------------------
`char`   | unicode codepoint | 32-bits   | 32-bits               | 0x000000 - 0x00D7FF & 0x00E00 - 0x10FFFF
`char7`  | 7-bit ANSI        | 8-bits    | 7-bit                 | 0x00     - 0x7F
`char8`  | 8-bit ANSI        | 8-bits    | 8-bits                | 0x00     - 0xFF
`char16` | utf-16 codepoint  | 16-bits   | 16-bits               | 0x0000   - 0xFFFF
`char32` | uft-32 codepoint  | 32-bits   | 32-bits               | 0x000000 - 0x10FFFF

Both the size and alignment of the characters are defined by their bit-width.
When used in a bitfield, specific bit-with mentioned above is used.

If a character has a value outside of its valid range, it is [illegal behavior].

> _Note_: Unlike [integer types], variably-sized boolean types do not support endianess



[integer types]:    ./integer-types.md
[illegal behavior]: ../../../illegal-behavior.md#character-types-