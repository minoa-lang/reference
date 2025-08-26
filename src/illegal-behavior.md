# Illegal behavior

Minoa has many operations which result in illegal behavior (IB). This can be compared to languages which have undefined beharvior (UB), illegal behavior is explicitly defined as invalid and will result in errors, instead of strange runtime behavior.

If illegal behavior is detected at compile time, the compiler will emit a compiler error and stop compiling.
If instead it happens during runtime, it will fall into one of two categories: safety checked and unchecked.

Unchecked illegal behavior, means that the compiler is not able to insert safety check for it. When this behavior is invoked at runtime, it's behavior is undefined.
The optimizer is free to make this illegal behavior do anything, which could lead from thing as simple as crashing, but may also execute code that should not be called, or even overwrite arbitrary data.
If unchecked behavior is executed at compile time, it will emit a compiler error, as it has much better infrastructure to catch issues like these.

Safety-checked illegal behaviors means that the compiler is able to insert a safety check of whereever this behavior can occur. At runtime, this will result in the program panicking.
The majority of illegal behavior is of this type.

For performance reasons, these safety checks may be enabled or disabled on a granular level, overriding the default for the current compilation mode.
This granularity works in 2 ways:
- on a safety check category level, and
- on a code level, from the entire library to statements.

This can be done using [safety-check attributes](#1717-safety-checks-)

> _Todo_: Add support for IB from contracts

> _Todo_: Add more examples: compile time & runtime

## Integer [↵](#illegal-behavior-)

Illegal behavior on integer operations can be caused by the below categories.

### Trunctation [↵](#integer-)

There are 2 ways of casting an integer that cause truncation:
- casting from a negative signed integer to a signed integer value
- casting a value of a larger bitwidth to a smaller bitwidth integer that cannot contain the value

#### Examples
```
// Casting a negative value to an unsigned type
let a: i32 = -23;
a as u32;

// Casting to a smaller bitwidth that cannot contain the value
let b: i16 = 300;
a as i878
```

To truncate a value, use the `<todo: insert fn name here>` function.

### Overflow/underflow  [↵](#integer-)

An integer value can overflow or underflow the possible value that can be contained within the integer type.
The following operators can cause an overflow or underflow:
- infix `+` (additon)
- infix `-` (subtraction)
- prefix `-` (negation)
- infix `*` (multiplication)
- infix `/` (subtraction)

To avoid overflow, either the wrapping, saturating or try variants of the operators could be used, or the associated core `..._with_overflow` function can be used.

> _Todo_: Should we add core functions here that might cause it too?

### Division by 0  [↵](#integer-)

An integer value division by 0 can be caused by the following operators:
- infix `/` (division) 
- infix `%` (remainder)

## Floating point [↵](#illegal-behavior-)

Illegal behavior of floating point operations can be caused by the below category

### Illegal operations [↵](#floating-point-)

Illegal floating point operations are operation that produce floating point exceptions.
These are slightly different to other illegal behavior, as they are actually defined within the [IEEE 754 specification](https://en.wikipedia.org/wiki/IEEE_754).

They are caused by operation that result in on of the following things:
- invalid operation: a mathematically undefined operation, e.g. `sqrt(-1)`
- division by 0: an operation on a finite operand that results in an exact infinite result, e.g. `1.0/0.0` or `log(0.0)`
- overflow: a finite result is too large to be represented accurately (i.e. its exponent with an unbounded exponenet range would be larger that the maximum exponent).
- underflow: a result is very small (outsize of normal range)
- inexact: the exact (i.e. unrounded) result is not represetable exactly.

In addition, some processors can also produce the following exception:
- denormal opearand: one of the operands passed to the instruction is a denormal value.

In addition, this also applies to certain functions when either operand is one of the following values
- `+inf`
- `-inf`
- `NaN` (quiet and signalling)

Or when their result does not fit in the destination size.

> _Note_: These illegal behavior depend on the floating point settings by the program.

> _Todo_: specify which functions can additionally cause FP exceptions

### Floating-point to integer out-of-bounds [↵](#floating-point-)

A floating point value can be out of bounds when trying to convert it to an integer value,
meaning that the value in the float cannot be converted to a valid integer value.

#### Examples
```
let f: f32 = 4294967296;
let i: i32 = f as i32;
```
## Memory [↵](#illegal-behavior-)

Illegal behavior on memory can be cause by the below categories

### Out-of-bounds [↵](#memory-)

Indexing memory out of bounds, thus resulting accessing an invalid memory location, is illegal behavior.
This can be caused by indexing into an array or a slice.

### Incorrect pointer alignment [↵](#memory-)

When assigning a memory address to a pointer, that memory address may not adhere to the alignment requirements of the pointer.
This also applies to references.

### Sentinel access [↵](#memory-)

When an array, slice, or multi-element pointer has a sentinel value, they can be written to and read from past the sentinel value.




## User-defined illegal behavior

> _Todo_