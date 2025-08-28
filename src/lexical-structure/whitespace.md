# Whitespace

Whitespace is uses to determine how lexical elements withing a file are interpreted, and used to
- separate seperate lexical elements
- decide how a mix of pre/post/in-fix operator is interpreted.

For any other purpose, whitespace is essentially ignored.
All whitespace is preserved in any reconstructed file.

Below are lists of all unicode characters recognized as either horizontal or vertical whitespace:
- Horizontal whitespace:
  - U+0009 CHARACTER TABULATION (horizontal tab / HT)
  - U+0020 SPACE
  - U+200E LEFT-TO-RIGHT MARK
  - U+200F RIGHT-TO-LEFT MARK
- Vertical whitespace:
  - U+000A: LINE FEED (newline / LF)
  - U+000B: LINE TABULATION (vertical tab / VT)
  - U+000C: FORM FEED (page break / FF)
  - U+000D: CARRIAGE RETURN (CR)
  - U+0085: NEXT LINE (unicode newline)
  - U+2028: LINE SEPARATOR
  - U+2029: PARAGRAPH SEPARATOR

These are essential the whitespace characters as are defined as `Pattern_White_Space` by Unicode.

A program has identical meaning if each whitespace element is replaces with any other legal whitespace character, such as a single space.
This does not include newline sequences which are mentioned below.

> _Note_: This is **not** a direct mapping to the unicode separator category `Z`
