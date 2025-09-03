
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

> _Warning_: Zero width non-joiner(ZWNJ U+200C) and zero width joiner(ZWJ U+200D) are not allowed in names.

> _Note_: A single `_` (underscore) is not a valid name

> _Note_: Names starting a double underscore '__' are reserved for the compiler or a runtime, user should not use any of these names, as this may cause issues when interal names have the same value.

## Extended names [↵](#names)
```
<ext-start-letter> := <start-letter> | ? unicode category Nd ?
<ext-name>         := <ext-start-letter> <letter>*
```

Unlike most languages, Minoa support what are so-called _extended names_, these are special locations where names are allowed to start with a digit.
Extended names are allowed in the following cases:
- a [path] segments, as long as it is not the first segment
- fields
- names in [field access expressions]

> _Example_:
> A usecase of this would be the following
> ```
> enum Dimension {
>     2D,
>     3D,
> }
> ```
> Either value can now be used like `.2D`, without requiring either an underscore to be added like `._2D` or longer names like `.Dimension2D` to be used.

> _Implementation node_: When an extended name start with a number, it will be parsed as a [decimal integer literal], followed by a regular [name]

> _Todo_: Link 'fields'

## Special names [↵](#names)
```
<implicit-parameter-name> := '$' <int-literal>
<meta-var-names>          := '$' <name>
```
In addition, some names are indicated with a leading `$`, these have special meanings.
These will either be:
- Implicit parameter names which may be introduced by [closure expressions] or [properties]
- meta-pattern variable names

> _Note_: `$` is also used to indicate the default format [template string], and to allow for pre/post-fix [operator methods] to be passed to closures

[name]:                     #names
[closure expressions]:      ../expressions/closure-expressions.md#shorthand-arguments-
[operator methods]:         ../expressions/closure-expressions.md#operator-methods-
[field access expressions]: ../expressions/field-access-expressions.md
[template string]:          ../expressions/template-string-expressions.md
[path]:                     ../identifiers-paths.md
[properties]:               ../items/properties.md
[decimal integer literal]:  ../literals.md#decimal-literal-