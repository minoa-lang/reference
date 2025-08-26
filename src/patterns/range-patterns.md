# Range patterns
```
<range-pattern>              := <exclusive-range-pattern>
                              | <inclusive-range-pattern>
                              | <from-range-pattern>
                              | <to-range-pattern>
                              | <inclusive-to-range-pattern>
<exclusive-range-pattern>    := <range-pattern-bound> '..' <range-pattern-bound>
<inclusive-range-pattern>    := <range-pattern-bound> '..=' <range-pattern-bound>
<from-range-pattern>         := <range-pattern-bound> '..'
<to-range-pattern>           := '..' <range-pattern-bound>
<inclusive-to-range-pattern> := '..=' <range-pattern-bound>
<range-pattern-bound>        := <number-literal>
                              | <char-literal>
                              | <path-expr>
``` 

Range expressions match any scalar value within the range of the bounds.
They are represented by a range operator with a value on at least one side.

An _exclusive range_ pattern matches all values from the lower bound up to, but **not** including, the upper bound.

An i_nclusive range_ pattern matches all values from the lower bound up to, **and** including, the upper bound.

A _from range_ pattern matches all values which are greater than or equal to the lower bound.

A _to range_ pattern amtches all value that are smaller than the upper bound.

The _inclusive to range_ patten matches all values that are smaller than or equal to the upper bound.


A bound may be any of the following:
- A character, byte character, integer, or floating point literal
- A `-`, followed by a character, byte character, integer, or floating point literal
- A path

If a bound is written as a path, it must point to a constant value with a character, integer, or floating point primitive type.

The lower bound may not be greater than the upper bound.

The range pattern matching any value of the same type as either of its bounds.
The bounds must therefore both be of the same type.

When using floating point literal, they must not have a type of `NaN`.

Patterns of a fix width primitive type are irrifutable, if the range spans the entirety of all possible values, e.g. `0..=255` for a `u8`.
The following patterns are irrifutable:
- An inclusive range from an integers minimum to maximum value
- A range of values that prececisly contains all supported characters, which are the following:
  - `char7`: `'\x00'..='\x7F'`
  - `char8`: `'\x00'..='\xFF'`
  - `char16`: `'\u{0000}'..='\u{D7FF}'` and `'\u{E000}'..='\u{FFFF}'`
  - `char`, `char32` : `'\u{0000}'..='\u{D7FF}'` and `'\u{E000}'..='\u{10FFF}'`

Range expression may not be used as a top level pattern within a slice a pattern, i.e. `[1.., a]` is **not** allowed

> _Todo_ Allow user-defined types supporting ranges as bounds ??
