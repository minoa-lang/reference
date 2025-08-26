# Panic

To allow a program terminate whenever it reaches an error that is not recoverable, the language provides a mechanism for this, a "panic".

The compiler may also insert panics for illegal behaviors, for example when accessing an index out of range.

The user also has control over how a panic occurs, mainly:
- a user overwritable panic handler
- control on how FFI functions should handle a panic.

## Panic handler [↵](#panic-)

A panic handler is a function with the `@panic_handler` attribute, and has the following signature:
```
@panic_handler
fn panic_handler(info: &PanicInfo) -> ! {
    ...
}
```

This allows the library providing the panic_handler to do whatever it wished to do wit hteh provided information.

### Standard behavior [↵](#panic-handler-)

The `std` library provides a standard panic handler, which can do either of the following
- unwind: unwinds the stack, allowing more information about the panic to be provided
- abort: aborts the process, which may be recoverable

> _Note_: 'unwind' is not supported on all platforms

If the library of program is compiled without the standard library, a custom panic handler needs to be provided.

## Unwinding [↵](#panic-)

Unwinding allows a panic to be recoverable, as any non-unwinding panic handlers will always be un-recoverable.
An unwinding panic handler however does not guarantee that the panic is recoverable, as it depends on its implementation.
Only the panic handler supplied by the standard library are guaranteed to be recoverable.

When a panic occurs, the unwind handler will "unwind" all stack frames, until it reaches a point where it can be recovered.
This also means that any object that implement `Drop` will have their `drop` functions called during the unwind.
This allows all the object to be cleaned up whenever a recoverable point is reached

> _Note_: Recovering from a panic should be avoided unless there is a good reason for it, for example, a VM which can restart processed on panic, like erlang's BEAM.

> _Note_: Compiling with unwind support will result in larger binaries

> _Todo_: Add links to parts in the std lib that can be used to handle it

### Unwinding over FFI boundaries [↵](#unwinding-)

It is possible to unwind across FFI boundaries using an appropriate ABI.
While this can be useful, it does require that the FFI library uses a matching unwind implementation and the program using it.
Mismatched implementation may cause unexpected behavior.

> _Todo_: Add more details about unwinding with uncompatible APIs

### Function unwinding linkage [↵](#unwinding-)

ABIs generally come in 2 flavors, a normal and an `-unwind` variant.
Generally both the `minoa` ABIs, with and without contexts, support unwinding, if enable when compiling those libraries.

> _Todo_: Add more info when the API/ABI has been figured out
