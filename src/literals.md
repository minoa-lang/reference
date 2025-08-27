# Literals
```
<literal> := <numeric-literals>
           | <boolean-literals>
           | <character-literal>
           | <string-literals>
```

A literal is a compile time constant representing a given value as defined below.

> _Note_: Literals are tokens and will therefore be parsed in the lexer stage_

> _Todo_: Specify how the literal values will be encoded, e.g. decimal values keeping all info so a literal operator can also know that a non-latin decimal character was used

## Numeric literals [↵](#literals-)

```
<digit_sep> := "_"
<numeric-literals> := <int-decimal-literal>
                    | <float-decimal-literal>
                    | <binary-literal>
                    | <octal-literal>
                    | <int-hexadecimal-literal>
                    | <float-hexadecimal-literal>
```

Numeric literals are literals representing a value of either an integer or floating-point type.

A common feature for integer literals are digit separators.
These don't effect the value represented, but can make the literals more readable to the programmer.
But at most a single digit seperator is allowed to be placed between 2 digits, multiple separators are not allowed, e.g.
Digit seperator also need to between 2 digits, so may not appear before the first digit, or after the last digit, in sequence.

```
1_000 // valid
1___000 // Error: multiple digit separators between 2 digits
_1000 // Error: digit seperator before first digit
1000_ // Error: digit seperator after last digit
```

Numeric literals have no limits on precision and may be seen as infinite precision values.
For this reason, no operations other than converting them to another type are allowed, as there is no known bound on how long such a calculation would take.
And example of this is `1/3`, which results in 0.33 repeating, meaning there is no bound on this calculation.

If a numeric literal is prefixed by either `+` or `-`, these will **not** be part of the literal, but will instead be an operator that is applied to the value.

There are generally 5 categories of numerics literals, and these are defined below.

### Decimal literal [↵](#numeric-literals-)

```
<dec-digit> := ? any unicode character in the category Nd ?
<int-dec-literal> := { <dec-digit> }+
                   | { <dec-digit> }+ ( 'e' | 'E' ) [ '+' ] { dec-digit }+
<float-dec-literal> := { <dec-digit> }+ '.' { [ <digit-serp> ] <dec-digit> }+ [ ( 'e' | 'E' ) [ '-' | '+' ] { dec-digit } { [ <digit-serp> ] <dec-digit> }* ]
                     | { <dec-digit> }+ ( 'e' | 'E' ) [ '-' | '+' ] <hex-digit> { [ <digit-serp> ] <hex-dec-digit> }
```

A decimal literal can represent either an integer or floating point value.
Decimal literals may be prefixed with `0`s without affecting the value, unlike some other languages, this does **not** get interpreted as an octal value and they are ignored.

Decimal literal work with any unicode codepoint representing a decimal digit, including leading 0s.

Floating points have a more complex representation.
They start with at least a single digit, and are then optionally followed by a decimal separator (`.`) and its fractional component.
After this, it is also possible to use scientific notation by writing an 'e' or 'E', followed by the exponent, this will modify the value before it by multiplying it by `10^exponent`.
The exponent is allowed to leave out the optiona `+` for positive exponent values.
The exponent is limited to the range -4932 to 4932 (values outside of this range will be clamped).

If a decimal literal with an exponent does not contain a decimal separator and has a positive exponent, this can also be interpreted as a integer literal.

A decimal separator always needs to be surrounded by a decimal digit, so as not to cause issues while parsing where the `.` could be a field access expression.
As tuple indexing on a decimal literal is not possible, this causes no confusion within the literal.

The exponent indicator `e` needs to be lower case to be consistent with the other indicators within literal values.

The integral and floating point decimal literals are of the type `core:.DecLiteral` and `core:.DecFloatLiteral` repespectively.

#### Examples [↵](#decimal-literal-)
```
// Integers
10
195
0042 // value of 42
٤٢ // Arabic-indic 42

// Floating point
0.5
128.64
3e10
005.2 // value of 5.2
۵.٢ // Arabic-indic 5.2
```

### Binary literals [↵](#numeric-literals-)

```
<bin-digit> := '0' | '1'
<bin-literal> := '0b' <bin-digit> { [ <digit-serp> ] <bin-digit> }*
```

A binary literal represents an integer value written as sequence of 0s or 1s, directly representing each bit in the resulting value.

If any character that represents a `<letter>` appears within the literal that is not supported, an error will be generated.

The binary literal indicator uses a lower case `b` for readability, as as uppercase `B` could be confused with `B`.

A binary literal is of type `core:.BinLiteral`.

#### Examples [↵](#binary-literals-)
```
0x1010 // decimal value 10
0x1100_0011 // decimal value 195
0x1_1 // decimal value 3
0x1111_1111_1111_1111_1111_1111_1111_1111_1111_1111_1111_1111_1111_1111_1111_1111 // u128::MAX
```

### Octal literals [↵](#numeric-literals-)

```
<oct-digit> := '0'-'7'
<oct-literal> := '0o' <oct-digit> { [ <digit-serp> ] <oct-digit> }*
```

An octal literal represents an integer value written as a sequence of octal values ranging from 0 to 7.

If any character that represents a `<letter>` appears within the literal that is not supported, an error will be generated.

The binary literal indicator uses a lower case `o` for readability, as an uppercase `O` could be confused with `0`.

An octal literal is of type `core:.OctLiteral`

#### Examples [↵](#octal-literals-)
```
0o12 // decimal value 10
0x303 // decimal value 195
0x3_0_3 // decimal value 195
0x377_7777_7777_7777_7777_7777_7777_7777_7777_7777_7777 // u128::MAX

```

### Hexadecimal integer literals [↵](#numeric-literals-)

```
<hex-digit> := '0'-'9' | 'a'-'z' | 'A'-'Z'
<int-hex-literal> := '0x' <hex-digit> { [ <digit-serp> ] <hex-digit> }*
```

A hexadecimal literal represents an integer value written as a sequence of nibbles, values ranging from 0 to 9, and then from A/a to F/f.
Mixing lower case and upper case letters is allowed, but is discouraged.
Currently a hexadecimal literal is limited to 32 digits, so not to overflow the maximum value of a 128-bit type.

If any character that represents a `<letter>` appears within the literal that is not supported, an error will be generated.

The binary indicator uses a lower case `x`, although no confusing with an uppercase `X` could occur, this is done to be consistent with both binary an octal indicators.

A hexadecimal literal is of type `core:.HexLiteral`

#### Examples [↵](#hexadecimal-integer-literals-)
```
0xA // decimal value 10
0xC3 // decimal value 195
0xC_3 // decimal value 195
0xFFFF_FFFF_FFFF_FFFF_FFFF_FFFF_FFFF_FFFF // u128::MAX

```

### Hexadecimal floating point literals [↵](#numeric-literals-)

```
<float-hex-literal> := '0x' ( '1.' | '0.' ) <hex-digit> { [ <digit-serp> ] <hex-digit> } <float-hex-exponent>
<float-hex-exponent> := 'p' [ '-' | '+' ] '0'-'9' { [ <digit-serp> ] '0'-'9' }*
                      | 'px' [ '-' | '+' ] <hex-digit> { [ <digit-serp> ] <hex-digit> }*
```

In addition to integer hexadecimal literals, there is also support to represent floating points as decimal literals.
These are composed out of a sign, a mantissa and an exponent.

The literal is written with a hexadecimal indicator `0x`. 
This can then be followed by either a `0.` or a `1.`.
After which the exponent indicator `p` appears, followed by an either `-` or `+`, and at the exponent in decimal digits.
Alternatively, if the exponent indicator `px` appears, the exponent is written with hexadecimal digits

When the literal starts with `0x0.`, both the mantissa and exponent are limited to 0.
The special values of 'SNAN', 'QNAN', '-INFINITY' or '+INFINITY' should not be encoded this way, for these values, the associated constant of the type should be used.

If any character that represents a `<letter>` appears within the literal that is not supported, an error will be generated.

The binary indicator uses a lower case `x`, although no confusing with an uppercase `X` could occur, this is done to be consistent with both binary an octal indicators.
The exponent indicator `e` also needs to be lower case for the same reason.

A hexadecimal floating point literal is of type `core:.HexFloatLiteral`

#### Examples [↵](#hexadecimal-floating-point-literals-)
```
0x0.0000000000000p0000 // value of 0
+0x0.0000000000000p+0000 // value of 0, but with included signs
-0x0.0000000000000p+0000 // value of -0
0x1.5555555555555p-2 // value of 1/3
0x1.5555_5555_5555_5p-2 // value of 1/3
0x1.0pxF // value of 1e15
```

## Boolean literals [↵](#literals-)
```
<bool-literal> := 'true' | 'false'
```

A boolean literal represents either a `true` of a `false` value.

A boolean literal is of type `core:.BoolLiteral`

## Character literals [↵](#literals-)

A character literal defines a character, represented by its unicode codepoints.

A character literal is of type `core:.CharLiteral`

```
<character-literal> := "'" ( ? any unicode codepoint, except for \ and ' ? | <escape-code> ) "'"
```

### Escaped characters [↵](#63-character-literals-)

```
<escape-code> := '\0'
               | '\t'
               | '\n'
               | '\r'
               | '\"'
               | "\'"
               | '\\'
               | '\p'
               | '\x' <hex-digit> <hex-digit>
               | '\u{' { <hex-digit> }[1,6] '}'
<string-escape-code> := <excape-code>
                      | '\p'
```

An escaped characterf, also known as an escape code, is used to represent certain character values that normally cannot be represented in a character or string.

These can be generally split into 3 categories:
- Simple escape codes
- Hex codes
- Unicode codepoints

A simple escape code exists out of a forward slash `/`, followed by single character.
The following escape codes are available:

code | Escaped codes
-----|---------------------------------------------------------------------------------------
`\0` | U+0000 (NUL)
`\t` | U+0009 (HT)
`\n` | U+000A (LF)
`\r` | U+000D (CR)
`\"` | U+0022 (QUOTATION MARK)
`\'` | U+0027 (APOSTROPHE)
`\\` | U+005C (BACKSLASH)
`\p` | Platform specific linebreaks, i.e. `\r\n` on windows and `\n` on unix-like systems

The special escaped character `\p` is only allowed within string literals, as it may result in a 2 character long sequence.

Hex codes can represent any 8-bit character value using a 2 digit hex code.
It is written as a `\x`, followed by an 2 hex digits.

By default, the hex code is limited to a value within the non-extended ASCII character set, which overlaps with the lowest values in unicode, meaning a value between `x00` and `x7F`.
This behavior can be changed, if a supporting literal operator, or template string expression is used.

Unicode codepoints represent any valid unicode codepoint, including surrogate pairs, this means all characters in the range 0x000000-0x10FFFF.
A unicode escape code is written as `\u{`, followed by between 1 and 6 hex digits, and closed of with a `}`.

#### Examples [↵](#escaped-characters-)
```
\n // Newline
\x61 // lower case 'a'
\x81 // Error: hex literal outside of the non-extended ASCII range
\u{1F44D} // '👍', thumb up emoji
```

## String literals [↵](#literals-)

```
<string-literal>         := <regular-string-literal> | <raw-string-literal>
<regular-string-literal> := '"' { ? any valid unicode codepoint, except for \ and '"' ? | ? string continuation sequence ? | <escape-code> | <string-interpolation> }* '"'
```

A regular string literal is a sequence of any unicode characters, enclosed by two `"` (double quote) characters, with the exception of `"` itself.
A string literal may also include include escaped characters.
They are also limited to being on a single line.

A string literal is of type `core:.StringLiteral`

> _Note_: String literals do not get impacted by normalization, unlike any other text in the source data, as defined in [Normalization]

### Multi-line string literals

```
<multi-line-string-literal>   := { <multi-line-string-segment> }* <string-literal>
<mulit-line=string=segment>   := '"' { ? any valid unicode codepoint, except for \, '"', and <new-line> ? | <escape-code> | <string-interpolation> }* ( <new-line> | <line-continuation-indicator> )
<line-continuation-indicator> := '\' <new-line>
```

A multi-line string is a special variant of a string literal that allows a string to be places accross multiple lines.
To keep to the rule that each line should be able to be independtly parsed without context of any other lines, multi line string are special, in that they exists out of multiple tokens.
Each segments is its own independent token, which start with a `"` (double quote), but ends on a new line, this indicated that the literal continues in the next token.
The multiline literal ends whenever a matching closing `"` is encountered.

As each line is required to start with a `"`, indentation inside of the string can easily be controlled, any preceeding indentation will be ignored, and any succeeding indentation will be part of the the string.

In addition, a single line can to be split up into 2 or more lines by adding a line continuation indicator, meaning that instead of having a newline inserted, the segments act as a single line.
A line continuation indicator is written as a `\`, followed by a new line sequence.

#### Examples [↵](#multi-line-string-literals)
```
"This is
"  a multiline
"string"

// Is equivalent to

"This is\n  a muliline\nstring"

// This will also result in the same string, as indentation before the starting `"` is ignored

  "This is
"  a multiline
  "string"
```

An example of a line continuation
```
"This \
"is on a\
" single line"

// Is equivalent to

"This is on a single line"
```

### Raw string literalss [↵](#string-literals-)
```
<raw-string-literal> := { '#' }[N] '`' { ? any valid unicode codepoint ? }* '`' { '#' }[N]
<raw-multi-line-string-literal> := { <raw-multi-line-string-literal> }* { <raw-string-literal> }
<raw-multi-line-string-literal> := { '#' }[N] '`' { ? any valid unicode codepoint, expect <new-line> ? }* <new-line>
```

Raw string literals are variants of a string literal which does not interpret escaped character specially, instead it interprets them as regular characters in text.
The raw string literal start with a sequence of `#` characters, and then finally a `` ` `` (backtic) and continues until it reaches another `` ` `` that is immediatally followed by sequence with a matching number of `#` is hit.
At minimum 0, and most 256 `#` characters are allowed.

Each segment is its own independent token, which must start with the same amount of `#`, followed by a backtick as the initial line, and ending on a new line, which indicates taht teh literal continues on the next line.
The multiline literal ends whenever a matching closing sequence is encountered.

> _Note_: Any line breaks within raw string literals are part of the resulting string.
> If the exact characters used to form line breaks are semantically relevant to an application,'
> any tools that reanslate line breaks in source code to different formats (between "\n" and "\r\n", for example) will chage application behavior.
> Developers should be carefull in sutch situations

#### Examples [↵](#raw-string-literals)
```
`this\n is one\n line`

// Is equivalent to 

"this\\n is one\\n line"


// nesting

#`outer `inner` outer again`#

```

Multi-line raw literals work line:
```
`muli-line
`raw
`string`

#`nested
#` `inner` 
#`multi-line`#

```

### String interpolation [↵](#string-literals-)
```
<string-interpolation> := '\' '{' <expr> [ ':' [ 'default' '=' <expr> ',' ] ? format specifiers ? ] '}'
```

A string interpolation allows expressions to be included within a string, allowing it to use the computed value, in addition to a set of format specifiers.
A string interpolation is indicated by `\`, followed by a block, which contains an expression and a format specifier, separated by a `:`.

In addition, when passing an optional type, a default value may be provided to the interpolation, this is done by having a `default` format argument, followed by the default value.A
A comma is then used to separate it from the format arguemnts

The expression used within the interpolation have no requirements by themselves, but these are set by the function or template string that uses them.
In cases where a string with an interpolation does not need special interpolation, this will be converted into just a string representation, as if the `\` was not present.

The format specifier may be any sequence of tokens, and its representation is interpreted by one or more formatters.


[Normalization]: ./source-representation.md#normalization