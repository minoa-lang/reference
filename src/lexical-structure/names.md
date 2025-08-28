
# Names
```
<start-letter> := '_' | ? unicode category XID_Start ? | ? unicode category Nd ?
<letter>       := ? unicode category XID_Continue ?
<name>         := <start-letter> <letter>*
```

A name is sequence of unicode points that can be used to identify a symbol or value in code.
Unicode support allows for the following names:
- `foo`
- `_identifier`
- `Москва`
- `東京`

Unlike most langauges, names are allowed to start with a number, as we do not support direct postfixes on numbers ('e'/'E' are special cases though).
The main rule is, that as long as a name does not also match either a keyword or a literal, it is a name.

Allowing names to start with a digit, can allow uses like:
```
enum Dimension {
    2D,
    3D,
}
```

> _Warning_: Zero width non-joiner(ZWNJ U+200C) and zero width joiner(ZWJ U+200D) are not allowed in names.

> _Note_: A single `_` (underscore) is not a valid name

> _Note_: Names starting a double underscore '__' are reserved for the compiler or a runtime, user should not use any of these names, as this may cause issues when interal names have the same value.

> _Todo_: Add ASCII only restrictions when needed.

# Special names
```
<implicit-parameter-name> := '$' <int-literal>
<meta-var-names>          := '$' <name>
```
In addition, some names are indicated with a leading `$`, these have special meanings.
These will either be:
- Implicit parameter names which may be introduced by [closure expressions] or [properties]
- meta-pattern variable names

> _Note_: `$` is also used to indicate the default format [template string], and to allow for pre/post-fix [operator methods] to be passed to closures

[closure expressions]: ../expressions/closure-expressions.md#shorthand-arguments-
[operator methods]:    ../expressions/closure-expressions.md#operator-methods-
[template string]:     ../expressions/template-string-expressions.md
[properties]:          ../items/properties.md