# Optional types
```
<optional-type> := '?' <type>
```

An optional type allows a value to be represented using a `null` or `.None` state, which can be used to represent a type with no value set.

Optional types allows the ability to have pointers that cannot be `null` ([`allowzero` pointers](./pointer-types.md#allowzero-) have a different meaning).

When an optional type (or the `Option(T)` type) is used, then depending on the value within, the compiler is allowed to do certain optimizations to encode the 'null' state within the value.
An example is a optional pointer, where the 'null' state is represented with an address of `0x00000000`.

This is synctactic suger of `Option(T)`, which comes with some additinal features, as the compiler understands nullable types. These are the following:
- `None` can be used to set the value to none, and `Some(...)` to set the value to a valid value, as they are imported via the core prelude
- when assigning a non-`.None` value, impliclty warps the value within `.Some(...)`. This also works for returns
- control flow expression have additional syntax to make using them more ergonomic
