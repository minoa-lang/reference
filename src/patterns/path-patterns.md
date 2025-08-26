# Path patterns
```
<path-pattern> := <path>
```

A path pattern can match any constant, or struct or enum member that have no fields.

They refer to any of the following:
- [enum](../type-system/types/enum-types.md) variants
- [Structs](../type-system/types/struct-types.md)
- [Constants](../items/consts.md)
- Associated constants

If the path contains any path disambigutation, it may only refer to an associated constant.

Path pattern are irrefutable if they point to a struct, an enum variant of an enum with only a single field, or a constant whose type is irrifutable.
