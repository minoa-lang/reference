# ABI, link, symbol, and FFI

These attribute can only be applied to [`extern` and `export`] blocks and their items, and are used to control how these items will behave.

> _Note_: These attributes do not specify how a specific library is linked, as multiple blocks would otherwise be able to conflict with eachother.
>         Instead, these are controlled by on of the following:
>         - command-line options to the compiler
>         - the project file
>         - using a build script

## `link` [↵](#abi-link-symbol-and-ffi-attributes)

The `link` attributes modifies how an `extern` or `export` item is linked, based on the metadata provided.

Some metadata value may have a different effect, depending on whether the item is marked as `extern` or `export`.

> _Note_: This is an `unsafe` attribute

### `prefix` and `suffix` [↵](#link-)

The `prefix` and `suffix` metadata is only allowed on [`extern` blocks].
As their name implies, the specify common prefixes and suffixes that will be appended to each item within the block when linking to it.

> _Example_
> ```
> @link(prefix = "sl_", suffix = "_fn")
> extern "SomeLib" {
> 
>     // will be linked to the function `sl_foo_fn`
>     fn foo();
> }
> 
> ```

### `name` [↵](#link-)

The `name` metadata can be applied to any items within either [`extern` or `export`] blocks.
This indicates the actual name within a library the item will be linked to, regardless of the name of the function within code.

> _Example_
> ```
> extern "SomeLib" {
>     // will be linked to the functiion `foobar`
>     @link(name = "foobar")
>     fn foo();
> }
> ```

### `ordinal` [↵](#link-)

The `ordinal` metadata can be applied to [`extern` or `export`] functions.
This indicates the index at which the function can be found within a library.

If this is supplied together with a `name`, the `oridinal` will only be used if the name indicated cannot be found in the library being linked.

> _Warniung_: The `ordinal` should only ever be used in cases where the ordinal of the symbol is stable:
>             if the ordinal of a symbol is not explicitly set when its containing library is built, then one will automatically be assigned to it,
>             and the assigned ordinal may change between different builds of the binary

> _Warning_: Not all libraries support ordinals

When using on an `export` function, the compiler will emit the function with the given oridinal within the produces binary.

> _Example_
> ```
> // the function will be bound to the function in the library with an ordinal of 12
> @link(ordinal = 12)
> fn foo();
> 
> // if the function cannot be linked via its name, it will instead use the function with the ordinal of 20
> @link(name = "bar_fn", ordinal = 20)
> fn bar();
> ```

### `decoration` [↵](#link-)

Some compilers, particularly those on windows, might add additional decorations to a function name.
The `decoration` metadata allows which decoration to be used.

It can have one of the following values:
- `.undecorated`: will not add any toolchain specific decoration to any link names
- `.no_prefix`: will add most toolchain specific decoration, specifically, it will skip all prefix decorations
- `.decorated`: will add all toolchain specific decorations

This is rarely used for any other library import method than a `raw-dylib`.
If the attribute is not present, the compiler will use the full decoration of the names.

Statics are never decoration, and thus ignore this attribute.

> _Note_: The exact decorations depend on the chosen toolchain.

> _Note_: This option is currently ignored on all platforms other than x86 windows

> _Example_
> ```
> // will end up having a name of `fn1@0` when using the MSVC toolchain
> @link(decoration = .no_prefix)
> fn fn1() {}
> ```

### `section` [↵](#link-)

The `section` metadata is used to specify the section within an object file where the item should be placed.

> _Example_
> ```
> // Puts the static in the .bss section, which contains uninitialized data
> @link(section = ".bss")
> extern static foo: i32;
> ```

## `used` [↵](#abi-link-symbol-and-ffi-attributes)

The `used` attribute may only be applied to [statics].
It tell the compiler to keep the static variable within the resulting binary, even when it is not used or reference anywhere else within the code.

> _Warning_: This does not prevent the linker from removing the symbol.

> _Example_
> ```
> // will be removed, as it is not used or referenced
> static FOO: i32 = 0;
> 
> // is kept, allowing, for example, an external binary to use the variable by dynamically looking this symbol up
> @used
> static BAR: i32 = 1;
> 
> // is kept, as it is a public symbol
> pub static BAZ: i32 = 2;
> 
> // is kept, as it's used in a public function
> static QUUX: i32 = 3;
> 
> pub fn get_quuz() -> i32 {
>     QUUX
> }
> 
> // will be removed, as it is only accessed via an unreferenced private function
> 
> static QUUZ: i32 = 4;
> 
> fn get_quuz() -> i32 {
>     QUUZ
> }
> ```

## `no_mangle` [↵](#abi-link-symbol-and-ffi-attributes)

The `no_mangle` attribute prevents the compiler from applying name mangling on the symbol, meaning the symbol will have the name provided in the resulting binary.

This additionally defines the item it is applied to as public, even when no `pub` explicitly visibility is provided.

This attribute is `unsafe`, as the unmangled symbol may collide with another symbol with the same name.
If this were to occur, this will result in an error

> _Example_
> ```
> // The function will be named `foo` within the resulting binary
> @no_mangle
> fn foo() {}
> ```

## `callconv` [↵](#abi-link-symbol-and-ffi-attributes)

The `callconv` attribute may only be applied to [functions].
It allows the calling convention which used when calling the function to be specified.

> _Example_
> ```
> // Function will be called using the `stdcall` calling convention instead of the default `C` calling conventions normally used by extern functions.
> @callconv("stdcall")
> extern fn foo();
> ```

## `contextless` [↵](#abi-link-symbol-and-ffi-attributes)

The `contextless` attribute may only be applied to non-[`extern` or `export`] [functions].
This specifies that call to the function do not carry the [implicit context] with them, preventing it from being used.
This attribute may also be applied on [methods].

_Example_
```
// prevent the implicit context from being used
@contextless
fn foo() {}

struct Bar {
    // can also be used on methods
    @contextless
    fn baz() {}
}
```



[functions]:             ../items/functions.md
[methods]:               ../items/functions.md#methods-
[statics]:               ../items/statics.md
[implicit context]:      ../implicit-context.md
[`extern` and `export`]: ../items/external-export-block.md
[`extern` or `export`]:  ../items/external-export-block.md
[`extern` blocks]:       ../items/external-export-block.md#external-blocks-