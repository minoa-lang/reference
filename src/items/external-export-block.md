# External & export blocks

Extern and export blocks allow for interactions with external code.

These blocks can be used to combine multiple external items within a group.

The block may also be supplied with attributes, these will be applied on the block will be applied to all supported items within the block.

When the [`@callconv` attribute] is applied to a block, it will propagate to all functions, but not statics, as they do not support this attribute.
Without this attribute, the calling convention will default to `C`.

`extern` blocks may contain the following items:
- [extern & export function]
- [extern & export statics]


## External blocks [↵](#external--export-blocks)
```
<extern-block>      := { <attribute> }* [ 'safe' ] 'extern' <string-literal> '{' { <extern-block-item> }* '}'
<extern-block-item> := <extern-block-static> | <extern-block-fn>
```

An `extern` blocks allows for code to use items that are declared in external code.

The `extern` block must explicitly provide the name of the library in which the items are located.
The compiler will automatically add a prefix and an extension to these names, which depend on the OS being compiled to.
These can be controlled via arguments provided to the compiler.

The `safe` specifier may be added to an `extern` block, which will propagate this specifier to all function declared within the external block.

## Export blocks [↵](#external--export-blocks)
```
<export-block>      := { <attribute> }* 'export' '{' { <export-block-item> }* '}'
<export-block-item> := <export-block-~static> | <export-block-fn>
```

An `export` block defines items which can be imported and called from external code.

They cannot be accessed from any code importing the items using a [`use` item].



[`use` item]:               ./use.md
[extern & export function]: ./functions.md#extern--exported-functions-
[extern & export statics]:  ./statics.md#extern--export-statics-
[`@callconv` attribute]:    ../attributes/abi-link-symbol-ffi.md#callconv-