# Path patterns
```
<path-pattern> := <path>
```

A path pattern can match any constant, or struct or enum member that have no fields.

They refer to any of the following:
- [enum] variants
- [Structs]
- [Constants]
- Associated constants

If the path contains any path disambigutation, it may only refer to an associated constant.

Path pattern are irrefutable if they point to a struct, an enum variant of an enum with only a single field, or a constant whose type is irrifutable.



[Constants]: ../items/consts.md
[enum]:      ../type-system/types/enum-types.md
[Structs]:   ../type-system/types/struct-types.md