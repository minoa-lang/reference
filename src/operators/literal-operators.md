# Literal operators

Literal operators are special operators which are directly applied at the end of a literal.
It allows a literal to be given an explicit type, in addition to producing more complex values or types.

The literal operator is written as an identifier which directly attaches to a type.

> _Example_
> ```
> // the type of `a` is f32
> a := 1.0f32;
> 
> // creates a runtime string
> text := "Hello world!"s;
> ```

## Literal operator declaration

```
<literal-fn-item> := { <attribute> }* [ <vis> ] [ 'const' [ '!' ] ] 'literal' 'fn' <name> '(' <name> ':' <type> ')' '->' 'LiteralError' '!' <type> <block>

<literal-item> := { <attribute> }* [ <vis> ] 'literal' <name> '[' <type> ']' '{' { <struct-elements> } '}'
```

A literal items is an item which may only appear within a [module].

The literal item defines a literal operator with the same name as the item.
Additionally, it declares the type of a literal to which the operator can be applied to.

The types on which the literal operator can be applied to must be one of the following `core` types:
- [`DecLiteral`]
- [`DecFloatLiteral`]
- [`BinLiteral`]
- [`OctLiteral`]
- [`HexLiteral`]
- [`HexFloatLiteral`]
- [`CharLiteral`]
- [`StringLiteral`]
- [`InterpStringLiteral`]

Each type corresponds to a given literal, as defined in the [literal section].

The item itself must contain 2 function, which adhere to the following template
```
literal <name>[<lit-type>] {
    const fn check(<name> : <lit-type>) -> LiteralError!() { ... }

    fn evaluate(<name> : <lit-type>) -> <ret-type> { ... }
}
```
where:
- `<name>` is any identifiers, the names may differ between the 3 locations
- `<lit-type>` is a supported literal type
- `<ret-type>` is the type produced by the literal operator

The functionality of the required functions is the following:
- `check`: Checks wether the literal contains a valid value for the type it is returning, emitting an error if the literal is invalid for the current representation.
           This is always run at compile time.
- `evaluate`: produces the literal value from the literal, by default at runtime.
              This may also be declared as `const`, which will run the function at compile-time and creates a constant.

## Core literal operators

Core literal operators are any literal operators that are provided by the `core` library.

literal operator | literal type    | resulting type | Info                                   | restrictions
-----------------|-----------------|----------------|----------------------------------------|--------------
`i8`             | Integral        | i8             | 8-bit signed integer literal           | n/a
`i16`            | Integral        | i16            | 16-bit signed integer literal          | n/a
`i32`            | Integral        | i32            | 16-bit signed integer literal          | n/a
`i64`            | Integral        | i64            | 16-bit signed integer literal          | n/a
`i128`           | Integral        | i128           | 128-bit signed integer literal         | n/a
`isize`          | Integral        | isize          | machine-sized signed integer literal   | n/a
`u8`             | Integral        | u8             | 8-bit unsigned integer literal         | n/a
`u16`            | Integral        | u16            | 16-bit unsigned integer literal        | n/a
`u32`            | Integral        | u32            | 16-bit unsigned integer literal        | n/a
`u64`            | Integral        | u64            | 16-bit unsigned integer literal        | n/a
`u128`           | Integral        | u128           | 128-bit unsigned integer literal       | n/a
`usize`          | Integral        | usize          | machine-sized unsigned integer literal | n/a
`f16`            | Float           | f16            | 16-bit floating point literal          | n/a
`f32`            | Float           | f32            | 32-bit floating point literal          | n/a
`f64`            | Float           | f64            | 64-bit floating point literal          | n/a
`f128`           | Float           | f128           | 128-bit floating point literal         | n/a
`b`              | `CharLiteral`   | u8             | Byte character literal                 | n/a
`b`              | `StringLiteral` | &[u8]          | Byte string literal                    | n/a
`c`              | `StringLiteral` | cstr           | C-string literal (null-terminated)     | all characters are required to have a codpoint of <=0x7F
`ansi`           | `StringLiteral` | str8           | ANSI string literal                    | all characters are required to have a codpoint of <=0x7F
`utf7`           | `StringLiteral` | str16          | UTF-7 string literal                   | all characters are required to have a codpoint of <=0x7F
`utf16`          | `StringLiteral` | str16          | UTF-16 string literal                  | n/a
`utf32`          | `StringLiteral` | str32          | UTF-32 string literal                  | n/a

In the above table, the kinds represent the following literal types
- Integral: `DecLiteral`, `BinLiteral`, `OctLiteral`, `HexLiteral`
- Float: `DecFloatLiteral`, `HexFloatLiteral`



[module]:                ../items/modules.md
[literal section]:       ../literals.md

[`DecLiteral`]:          #literal-operators "Todo: link to docs"
[`DecFloatLiteral`]:     #literal-operators "Todo: link to docs"
[`BinLiteral`]:          #literal-operators "Todo: link to docs"
[`OctLiteral`]:          #literal-operators "Todo: link to docs"
[`HexLiteral`]:          #literal-operators "Todo: link to docs"
[`HexFloatLiteral`]:     #literal-operators "Todo: link to docs"
[`CharLiteral`]:         #literal-operators "Todo: link to docs"
[`StringLiteral`]:       #literal-operators "Todo: link to docs"
[`InterpStringLiteral`]: #literal-operators "Todo: link to docs"