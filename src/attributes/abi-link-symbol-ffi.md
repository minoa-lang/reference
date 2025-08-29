# ABI, link, symbol, and FFI

These attribute control how `extern` and `export` items will be managed

> _Note_: To control how a specific library is linked, use either command line options or a `build.mn` script

# `link_prefix` [↵](#abi-link-symbol-and-ffi-attributes)

The `link_prefix` attribute set a common prefix for all link names whithin a block

# `link_suffix` [↵](#abi-link-symbol-and-ffi-attributes)

The `link_suffix` attribute set a common suffix for all link names whithin a block

# `link_name` [↵](#abi-link-symbol-and-ffi-attributes)

The `link_name` attribute is used to specify the link name of an external function or static.

# `link_ordinal` [↵](#abi-link-symbol-and-ffi-attributes)

The `link_ordinal` can be used to specify the numeric ordinal of an external function or static.
The ordinal is a unique number identifying a symbol exported by a dynamic library on windows and can be used when the library is being loaded to find that symbol rather than having to look it up by name.

> _Warning_: The `link_ordinal` should only be used in cases where the ordinal of the symbol is stable: if the ordinal of a symbol is not explicitly set when its containing binary is built, then one will automitically be assigned to it, and that assigned oridinal may change between builds of the binary.

> _Warning_: Not all libraries support ordinals

# `repr` [↵](#abi-link-symbol-and-ffi-attributes)

The `repr` trait controls the type layout as defined in the [layout representation section]

# `export_name` [↵](#abi-link-symbol-and-ffi-attributes)

The `export_name` attribute specifies the name of the symbol that will be exported on a function or static.

# `link_section` [↵](#abi-link-symbol-and-ffi-attributes)

The `link_section` attribute specifies the section of the object file that a function of static's content will be placed into.

# `no_mangle` [↵](#abi-link-symbol-and-ffi-attributes)

The `no_mangle` attribute disables name mangling and will output a symbol with the same name as  the function or static.

# `used` [↵](#abi-link-symbol-and-ffi-attributes)

The `used` attribute can only be applied to static items.
This attribute is used to keep the variable in the output object file, even if the variable is not used or referenced by any other item inside the library.
However, the linker is still free to remove such an item.

# `callconv` [↵](#abi-link-symbol-and-ffi-attributes)

The `callconv` attribute defined which calling convention a function will use when called.

# `contextless` [↵](#abi-link-symbol-and-ffi-attributes)

The `contextless` attribute defines a function as running without access to the implicit context, maning that it will use the `contextless` ABI without having to be declared as [`extern` or `export`].
This means that this also can be applied on [methods].



[`extern` or `export`]:                    ../items/functions.md#external--exported-functions-
[methods]:                                 ../items/functions.md#methods-
[layout representation section]:           ../type-system/type-layout/layout-representation.md