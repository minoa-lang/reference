# Extensions

```
<extern-block> := { <attribute>* } [ 'safe' ] `extern` <string-literal> '{' <extern-static-item> | <extern-block-fn-item> '}'
<export-block> := { <attribute>* } `export` '{' <extern-static-item> | <extern-block-fn-item> '}'
```

External and export blocks allow their corresponding version of function and static variables to be declared, with all of them having the same attributes applied to them.

An `extern` block can import items from an external libary, they define the name of the library in which the item is located.
The compiler will automatically add the file extension for a given OS.

Extern items will by default be marked as `unsafe`, but can be explicitly marked as `safe`.

An `export` block on the other hand will mark an item for export.

Either can have additional info that is supplied using their corresponding [attributes](../attributes.md#abi-link-symbol-and-ffi-attributes-).
If applied to a block, it will apply to all items within that block.

The items also have their own respective properties as defined for [external functions](../items/functions.md#external--exported-functions-) and [external statics](../items/statics.md#external-statics-).

All items within an external block share the same source library, defined in the block's declaration.

The external block may define the default calling convention using the [`@callconv` attribute](../attributes.md#callconv-).
