# Notation

This section contains the notation used to define the grammar within the reference.

notation           | meaning                                                | examples
-------------------|--------------------------------------------------------|--------------------------------
`...`              | Notation placeholder                                   | n/a
`... := ...`       | Definition                                             | `<a> = 'a'`
`... ...`          | Sequence of gramatical elements                        | `'a' 'b'`
`\<name>`          | Gramatical element                                     | `'<literal>'`
`'...'` or `"..."` | literal text                                           | `'fn', 'match'`
`U+...`            | Unicode character                                      | `U+000A`
`... | ...`        | alternative                                            | `'x' | 'y'`
`( ... )`          | grouping                                               | `( 'a' | 'b' )`
`[ ... ]`          | optional                                               | `[ a ]`
`{ ... }+`         | one or more repetition                                 | `{ 'a' }+`
`{ ... }*`         | zero or more repetitions                               | `{ 'a' }*`
`{ ... }[N]`       | `N` repetitions (relative to other elements)           | `{ 'a' }[N]`
`{ ... }[N,M]`     | `N` to `M` repetitions (where `N` and `M` are numbers) | `{ 'a' }[1,6]`
`? ... ?`          | Special values defined within the `?`s                 | `? Value explenation here ?`
`\ ... \`          | Pattern, single token (follows notation rules)         | `\f(32|64)\`
`... - ...`        | Range                                                  | `'a'-'z'`

## Implicit groups

Whenever 2 different representations are on 2 separate lines, and the latter one start with `|`, this means the entire line is a group on its own.

This means that
```
<val> := 'a' 'b'
       | 'c' 'd'
```
is equivalent to the following
```
<val> := ( 'a' 'b' ) | ( 'c' 'd' )
```
and not
```
<val> := 'a' ( 'b' | 'c' ) 'd'
```
i.e. there are just 2 valid combinations that match: `ab` or `cd`