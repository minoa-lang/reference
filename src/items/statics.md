# Static items
```
<static-item> := { <attribute> }* [ <vis> ] [ [ 'mut' ] 'tls' ] 'static' <name> [ ':' <type> ] '=' <expr> ';'
<extern-static-item> := { <attribute> }* [ <vis> ] [ 'mut' ] [ 'tls' ] 'static' <name> [ ':' <type> ] ';'
```

A static item is a named location within the program's static memory.
All references to a static refer to the same memory location.

Static items live for the entirety of the programs life and are never dropped at the end of the program.
If a static has a droppable value, it is up to the programmer to make sure the data is dropped at the end of the program.

Any 0-sized static variable will take up at minimum 1-byte.

Static items must be initialized using an expression that can be evaluated at compile time.

Non-mutable static items that do not support interior mutability and will be allocated in read-only static memory.

All access to statics is safe, but there are a number of restrictions:
- The type must have a `Sync` trait bound to allow thread-safe access.
- Statics may not be refered to from a constant.

## Thread local storage [↵](#static-items)

Static values may also be allocated as thread local storage, using the weak `tls` keyword before the `static` keyword.
Tls statics are unique to the thread they are running on and are not shared with other threads.

Unlike static items, a thread local static can be mutable without requiring [interior mutability], as it can only be accessed from the current thread.

## Statics and generics [↵](#static-items)

When a static variable is declared within a generic scope, it will result in exactly 1 static item being defined, shared accross all monomorphization of that scope.

## External statics [↵](#static-items)
```
<extern-static>       := { <attribute> }* [ <vis> ] 'extern' <string-literal> ['mut']  <name> ':' <type> ';'
<extern-block-static> := { <attribute> }* [ <vis> ] ['mut']  <name> ':' <type> ';'

<export-static>       := { <attribute> }* [ <vis> ] 'export' ['mut']  <name> ':' <type> '=' <expr> ';'
<export-block-static> := { <attribute> }* [ <vis> ] ['mut']  <name> ':' <type> '=' <expr> ';'
```

Statics can be declared as `extern` or `export`, allowing interaction with external code.
If a static is `extern`, it cannot have an explicit value, meanwhile `export` static must have an initial value.

Unlike normal statics, both `extern` and `export` statics are allowed to be declared mutable, without needing to rely on interior mutability.
An immutable static must be initialized before any code is executed.


[interior mutability]: ../type-system/interior-mutability.md