# Static items
```
<static-item> := { <attribute>* } [ <vis> ] [ <static-tls> | 'lazy' ] 'static' <name> [ ':' <type> ] '=' <expr> ';'
```

A static item is a named location located in a program's static memory.
All references and pointers to a static item refer to the same memory locations.

Static variables must be initialized with an compile-time expression, similar to [constant items].
The initializer may use any other static variable within its initializer, which will use the initial value of those constants.

Static items live for the entirety of the program's life and are never dropped at the end of the program.
If a static has a droppable value, it is the user's responsibility to ensure the value is dropped.

If the type of a static variable is 0-sized, it will take up at least 1 byte within memory, as each static variable is required to be located at a unique address.

Whenever a static item does not have interior mutability, it will be stored in read-only memory.

All access to statics is safe, but there are a number of restrictions:
- The type must support the relevant traits to allow thread-safe access
- Statics may not be references from a constant value

> _Todo_: List 'relavent traits"

## Thread local starage [↵](#static-items)
```
<static-tls> := [ 'mut' ] 'tls'
```

Static may also be stored within Thread Local Storage (TLS).
Each static will be unique to thread it is run on, and therefore is guaranteed not to interact with other threads.

Because of this fact, `tls` statics may be mutable, without requiring [interior mutability], as it is only ever modifyable by the current thread.

## Lazy statics

A `lazy` static is a static value which is only initialized on the first use of the static within code.
This allows a lazy static to be intialized using non-compile-time code.

This initialization is done in a thread-safe way, so the value will never be initialized twice.
However, this does require the static value to take up additional space in memory, which will increase the size by 1 times its alignment.

2 `lazy` statics amy not be circularly dependent on each other.

## Statics & Generics [↵](#static-items)

Static items may be defined within a type using generics.
This will result in a single static which will be the same across any instance of the type.

> _Example_
> ```
> struct Foo<T> {
>     static COUNTER: Wrapper<u32> = Wrapper.new(0);
> 
>     fn do_foo() {
>         println("counter is \{COUNTER.get_and_inc()}");
>     }
> }
> 
> Foo<i32>.do_foo();
> Foo<String>.do_foo();
> ```
> will output
> ```
> counter is 0
> counter is 1
> ```

> _Example_
> This also works for default trait imp and any generic implementation of it:
> ```
> trait Tr {
>     fn default_impl() {
>         static COUNTER: Wrapper<u32> = Wrapper.new(0);
>         println("def_impl(): counter is \{COUNTER.get_and_inc()}");
>     }
> 
>     fn blanket_impl();
> }
> 
> struct T0;
> struct T1;
> 
> impl[T] T as Tr {
>     fn blanket_impl() {
>         static COUNTER: Wrapper<u32> = Wrapper.new(0);
>         println("blanket_impl(): counter is \{COUNTER.get_and_inc()}");
>     }
> }
> 
> T0.(Tr.default_impl)();
> T1.(Tr.default_impl)();
> T0.(Tr.blanket_impl)();
> T1.(Tr.blanket_impl)();
> ```
> will output
> ```
> default_impl(): counter is 0
> default_impl(): counter is 1
> blanket_impl(): counter is 0
> blanket_impl(): counter is 1
> ```

## Extern & Export statics [↵](#static-items)

Statics can be declared as `extern` or `export`, allows interaction with external code.

Unlike normal statics, both `extern` or `export` statics are allowed to be declared as `mut` without needing to rely on interion mutability.
This does require that all operation on these statics must be done in an unsafe context.

### Extern [↵](#extern--export-statics-)
```
<extern-static>       := { <attribute> } [ <vis> ] 'extern' <string-literal> [ 'mut' ] <name> ':' <type> ';'
<extern-block-static> := { <attribute> } [ <vis> ] [ 'mut' ] <name> ':' <type> ';'
```

An `extern static` allows code to reference values declared in an external library that is not imported using a [`use` item].

An `extern static` cannot have an initial value, as it relies on the external code providing the current value of the static.

When not declared inside of an [extern block], the `extern static` must explicitly provide the name of the library in which it is located, otherwise it will use that of the extern block.
The compiler will automatically add a prefix and an extension to these names, which depend on the OS being compiled to.
These can be controlled via arguments provided to the compiler.

### Export
```
<export-static>       := { <attribute> } [ <vis> ] 'export' [ 'mut' ] <name> ':' <type> ';'
<export-block-static> := { <attribute> } [ <vis> ] [ 'mut' ] <name> ':' <type> ';'
```

An `export static` defines a static which can be imported and called by external code.

They cannot be accessed from any code importing the value using a [`use` item].


[constant items]:      ./consts.md
[extern block]:        ./external-export-block.md
[`use` item]:          ./use.md
[interior mutability]: ../type-system/interior-mutability.md