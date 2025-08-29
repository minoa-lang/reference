# Debug

Debug attributes allow for additional debug information to be specified for a given item.

# `debugger_visualizer` [↵](#debug-attributes-)

The `debugger_visualizer` attribute can be used to embed debugger visualizer info into the debugging information.
This enables an improved debugger experience for displaying values in the debugger.

The attribute exists out of a `kind` and either a `file` or `inline` specifier.

The `kind` specifier can be one of the following
- `natvis`: XML-based natvis for microsoft debuggers. More detail on the format can be found in Microsoft's [natvis documentation].
- `gdb`: GDB uses a python script based visualizer. More details on the format can be found in GDB's [pretty printing documentation].
- `minoa`: Minoa specific debug visualization (not supported yet)

The actual visualization can be specified in 2 ways:
- `file`: the visualization is specified in an internal file, this contains a path to it.
- `inline`: the visualization is specified inline inside of the code file



[natvis documentation]:                    https://learn.microsoft.com/en-us/visualstudio/debugger/create-custom-views-of-native-objects?view=vs-2022
[pretty printing documentation]:           https://sourceware.org/gdb/current/onlinedocs/gdb.html/Pretty-Printing.html