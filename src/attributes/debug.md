# Debug attributes

Debug attribute allow for additional debug information to provided to the fragment they are applied on.

## `debug_visualizer` [↵](#debug-attributes)

The `debug_visualizer` attribute may be applied to any [composite type], or directly in the [main module].
It enabled an improved debugger experience by providing a way to inform a debugger of how the type should be displayed to the user.

The attribute currently support 3 debugger formats:
- `natvis`: XML-based natvis for microsoft debuggers.
            More information on the format may be found in Microsoft's [natvis documentation]
- `gdb`: python script for GDB-like debuggers.
         More information on the format may be found in GDB's [pretty printing documentation].
- `minoa`: Allows debugger information to be provided using code written directly in minoa (currently not supported yet).

The visualization may be provided in 2 different forms:
- `file`: the visualization is provided with a path to a file containing the visualizer's representation.
- `inline`: this visualization is provided directly within the attribute. This must always be provided within parentheses.

By default, the attribute will select the correct form depending on wether a [string literal] or sequence of tokens is provided.

> _Example_
> ```
> @debug_visualizer(natvis = "foo_vis.natvis", gdb = "foo_vis.py")
> struct Foo {
>     x, y: i32
> }
> 
> @debug_visualizer(natvis(
>     <?xml version="1.0" encoding="utf-8">
>     <AutoVisualizer xmlns="http://schemas.microsoft.com/vstudio/debugger/nativs/2010">
>         <Type Name="Foo">
>             <DisplayString>({x}, {y})</DisplayString>
>             <Expand>
>                 <Item Name="x">x</Item>
>                 <Item Name="y">y</Item>
>             </Expand>
>         </Type>
>     </AutoVisualizer>
> ))
> struct Bar {
>     x, y: i32
> }
> ```

[string literal]:                ../literals.md#string-literals-
[main module]:                   ../package-structure.md#main-module-
[composite type]:                ../type-system/types/composite-types.md

[natvis documentation]:          https://learn.microsoft.com/en-us/visualstudio/debugger/create-custom-views-of-native-objects?view=vs-2022
[pretty printing documentation]: https://sourceware.org/gdb/current/onlinedocs/gdb.html/Pretty-Printing.html