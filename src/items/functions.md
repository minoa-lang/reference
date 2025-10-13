# Functions
```
<fn-item>       := <fn-qualifiers> 'fn' <name> <fn-signature> { <contract> }* <fn-body>
                 | <template-string-fn>
<fn-qualifiers> := { <attribute> }* [ <vis> ] [ ( [ ( 'const'  [ '!' ] ) | 'async' ] [ 'unsafe' ] [ 'gen' ] ) ]
<fn-signature>  := [ <deduced-params> ] '(' [ <fn-params>  [ ',' ] ] ')' [ <fn-return> ] [ <where-clause> ]
```

A functions constist of a named block of code, togehter with a set of parameters (both implicit and explicit), and a return type, which represents a unit of reusable code.

When refered to, a function yields the corresponding zero-sized [function type], which, when called, evaluates into a direct function call.

> _Todo_: Effects

## Function indentifiers [↵](#functions)

Each function is identified by both it's name, and its parameter labels.
This identifier is used to look up the function at its call site.

The identifier consists out of the function's name, followed by a by the list of parameter names in parentheses, separated by a `:`.
Optional parameters get a `?` prepended to them, and variadic parameters will be followed by a `...`.

> _Note_: the return of a function has no impact on its identifier

> _Examples_
> ```
> fn foo() {} // -> foo()
> fn foo(a: i32, b: i32) {} // -> foo(a:b)
> fn foo(_ a: i32, _ b: i32) {} // -> foo(_:_)
> fn foo(a: i32, _ b: i32, c: i32, c d: i32) {} // -> foo(a:_:_:c)
> 
> // With optionals
> fn foo(a: i32, b: i32 = 3) {} // -> foo(a:?b)
> fn foo(a: i32 = 1, b: i32 = 2) {} // -> foo(?a:?b)
> 
> // With variadics
> fn foo(a: i32, b: i32...) {} // -> foo(a:b...)
> 
> // With optionals and variadics:
> fn foo(a: i32, b: i32 = 2, c: i32...) {} // -> foo(a:?b:c...)
> ```

## Parameters [↵](#functions)
```
<fn-params>            := <fn-param> { ',' <fn-param> }*
<fn-param>             := <fn-req-param>
                        | <fn-opt-param>
                        | <fn-variadic-param>
<fn-req-param>         := [ <fn-param-label> ] <fn-param-name>  ':' [ <fn-param-specifiers> ] ( <type> | <raw-closure-type> )
<fn-param-name>        := <name> { ',' [ <fn-param-label> ] <name> }
                        | <pattern-top-no-alt>
<fn-param-type>        := <type> 
                        | <type-bound>
```

A function parameter represents one or more values that can be passed to a function.
Each parameter can be specified as either:
- a collection of one or more comma-separated names, where each can be made mutable or constant, or
- an irrefutable pattern

If multiple names are provided, the parameter will be split up into as many distinct parameters as names provided.

When a pattern is provided, a value passed to the parameter will be irrifutably destructure the data into the pattern.
```
// Will destructure the enum passed to its individual arguments
fn name((value, _): (i32, i32)) -> i32 { value }
```

If any parameter has a type of `type`, it must be marked as `const`.

### Parameter specifiers [↵](#parameters-)
```
<fn-param-specifiers>  := [ 'mut' | 'const' | 'lazy' | 'move' ]
```

Each parameter may be suplied with an additional specifier, this specifier is located between the `:` and type.

The specifier can be one of the following:
- `mut`: allows the variable to be directly modified
- `const`: takes the parameter in as a compile-time parameter, which will be interpreted as a generic function parameter
- `lazy`: takes in the parameter as if it were an [autoclosure] producing a value of the type this parameter has, after the initial use, this parameter will then be available as if it was like any other parameter.
- `move` indicates that the argument will be moved into the function, regardless or whether it implements `Copy` or not.

When any of these specifiers are applied to a pattern, this will count for all bindings within it.
If a `const` specifiers is provided, the pattern may not contain any `mut` bindings.
The `lazy` attribute applies to the entire pattern, meaning that if one binding is used, all bindings will have their value directly available.

> _Implementation note_:
> Any arguments passed to a function, even with `mut` applied to it, is inherently immutable, meaning that a function with a `mut` parameters, like:
> ```
> fn foo(val: mut i32) { ... }
> ```
> is equivalent to
> ```
> fn foo(val: i32) {
>     mut val := val;    
> }
> ```

### Parameter labels [↵](#parameters-)
```
<fn-param-label> := <ext-name> | ? <strong-kw>, except 'const' or 'mut' ? | ? <weak-kw>, excepct for `lazy` ?
```

Parameter labels are used to name arguments when calling a function.
They additionally allow different functions with the same name, but different parameter labels, to be distinguished.

If this label is provided, it can either be:
- a name, this will be used as the label for the argument when calling the function, or
- an `_`, this indicates a label-less parameter, meaning that the argument in this position can be passed without needing to specify its label in the call

If no label is provided, it will default to the following:
- if the parameter has a name, it will use this name as the label, e.g. `foo: i32` will become `foo foo: i32`
- if the parameter has a pattern, it will be a label-less parameter

> _Example_
> ```
> fn foo(struct const ty: type, in container: Bar);
> ```

In addition, since the order of the parameters still matters, label do not have to be unique, with 1 exception.
Whne a required parameters follows an option label, it cannot have the same name as the optional parameter, unless separated by another labeleled required parameter.

> _Example_
> ```
> // allowed: identical names for required parameters
> fn add(a: i32, and b: i32, and c: i32) { ... }
> // allowed: optional value does not have the same name as the next required parameter
> fn add(a: i32, maybe b: i32, and c: i32) { ... }
> // error: parameter `c` has the same label as the preceeding optional parameter `b` ("and")
> fn add(a: i32, and b: i32 = 0, and c: i32) { ... }
> ```


> _Note_: There is not explicit syntax that introduced the label, as possible collisions are very rare.
> There is however 1 occasion where this could happen: [tuple struct pattern].
> For this, the compiler relies on whether there is whitespace between the name and the `(` character, i.e.
> ```
> // `name` is a label
> fn (name (x, y): (i32, i32)) {}
> 
> // error: `name` is part of a 'tuple struct pattern', which does not have a tuple type
> fn (name(x, y): (i32, i32)) {}
> ```

### Optional parameters [↵](#parameters-)
```
<fn-opt-params> := <fn-opt-param> { ',' <fn-opt-param> }*
<fn-opt-param>  := <fn-param> '=' <expr>
```

An optional parameter is a parameter which is provided with a default value, in case no explicit value is given when calling a function.
Optional parameters may appear anywhere in the function order, the only exception is that a label-less optional may not preceed a label-less required parameter.

The default value needs to be a compile-time known expression.

> _Note_: While not required, it's recommended to place optional behind all required parameters whenever it makes sense

> _Note_: To default to a runtime expression, pass the parameter in as a `?T` type and use the supplied `.unwrap_or(x)` function

### Variadic parameters [↵](#parameters-)
```
<variadic-param> := <fn-param-name> ':'  <type> '...' [ '=' <expr> ]
```

A variadic parameter allows for 0 or more values of the the parameter pack's type to be passed to a function.
There therefore allow an unknown amount of paramters to be passed.

When providing a default value, multiple default values may be passed within an array.

> _Example_
> ```
> fn foo(values: i32... = [ 1, 2, 3 ]) {}
> ```
> also works with arrays
> ```
> fn foo(values: [2]i32... = [ [1, 2], [3, 4] ]) {}
> ```

If a label-less variadic parameter is provided, it must be the last argument within a function.
If instead, a variadic parameter with a label is provided, it may be located anywhere within the function signature.
Any variable after a variadic parameter **must** have a label

When calling a function with a variadic parameter, each value passed is listed as a comma-separated list of values.
In addition, if the variadic has tuple value, each element may be a separate values within the comma-separated list, and must not be put in a tuple.
For more info and examples, see the [call expression].

> _Note_: the exact utilities to work with variadic arguments are not yet defined

### Function closure parameters [↵](#parameters-)

Function closure parameters are special parametes that have the type of a [raw function type].

They introduce an anonymous deduded type within the signature, which implements the given interface, as as indicated by the raw function type.

> _Example_
> ```
> fn foo(pred: fn(i32) -> bool) { ... }
> ```
> would translate to
> ```
> fn foo[C: is Fn(i32) -> bool](pred: C) { ... }
> ```
> where `C` is an anonymous type which cannot be explcitly referred to.

### Deduced parameters [↵](#parameters-)
```
<fn-deduced-params> := '[' <fn-deduced-param> { ',' <fn-deduced-param> } ']' [ ',' ]
<fn-deduces-param>  := <name> ':' <type>
```

A deduced parameters is a special kind of parameter that is directly deduced from other parameters.
All deduced parameters are automatically `const` parameters.

> _Example_
> ```
> // Both `T` and `N` can be deduced from the value passed into `arr`
> fn foo[T: type, N: usize](arr: [N]T) {}
> ```

If a deduced parameter cannot get enough info from the rest of the function to deduce its value, it will result in a compile error

> _Example_
> ```
> // error: annot deduce the type of `U` from the signature
> fn foo[T: type, U: type](val: &T) -> U {}
> ```

## Returns [↵](#functions)
```
<fn-return> := '->' ( <type> |<fn-named-ret> )
```

A function can also return a type, which is indicated by the return value.

This value can be returned via either a [`return`], a [`yield`], or an [implicit return] at the end of the function's block.

If no explicit return type is provided, the function will return a [unit type].

### Named returns
```
<fn-named-ret>      := <fn-named-ret-elem>
                     | '(' <fn-named-ret-elem> { ',' <fn-named-ret-elem> }* [ ',' ] ')'
<fn-named-ret-elem> := <name> ':' <type> [ '=' <expr> ]
<fn-named-ref-def>  := <literal-expr>
                     | <path-expr>
                     | <paren-expr>
                     | <constructing-expr>
                     | <block-expr>
```

Return values can also be named, this allows the return values to be explicitly assigned within the function.
These values can then be returned by a plain [`return`] or [`yield`], or can also be returned if the body does not contain an [implicit return].
It is however still possible to explicitly return a different value.

In addition, a default value can be provided for each named return.
This value will only be assigned in any return path where not value is explicitly is assigned, or returned from the function.

The expression for the default value may be a runtime value, but is limited to one of the following expressions:
- [literal expression]
- [path expressions]
- [parenthesized expressions]
- [constructing expressions]
- [block expression]

It is not allowed to shadow any named defined within a named return, except when done inside of a [nested item].

When a possible return path does not assign a value to a named variable, or is not returned, it will result in a compile error.

When multiple named returns exists, the function will return a [named tuple].

### Raw function return types

When a [raw function type] is present within the return type, it will represent an [impl trait type] that implements the given, as as indicated by the raw function type.

## Function body [↵](#functions)
```
<fn-body> := <block>
           | '=>' <expr> ';'
```

A function body cotnains the acutal code associated that is executed when calling the functions.

It conceptually is a block expression which resides with another block which declares both the parameters (including assigning them) and named returns of the function, which returns the value of the body.
Since it's conceptually a [block expression], this also means that the body will return any [implicit return] at the end of the block.

> _Example_: Conceptual representation of a function body
> ```
> fn foo(val: i32) { val }
> ```
> could be seen as
> ```
> {
>     val := argument0;
>     return {
>         val
>     };
> }
> ```

Within this surrounding block, any pattern taken in by a parameter are also assigned

> _Example_
> ```
> fn foo(.{ a: val }: A) {}
> ```
> could be seen as
> ```
> let A{ a: val } = argument0;
> ...
> ```

In addition, a 'short function body, also known as an "inline function body", is also supported, allowing simple function bodies to be written as a simple expression associated with the function.

_Example_
``` 
fn add(a, b: i32) -> i32 => a + b;
``` 

## Unsafe functions [↵](#functions)

A function may be declared as `unsafe`, this requires that the function is called from an [unsafe context].
This does however not mean tht the body of the function is an unsafe context, any calls to unsafe functions must still be located within an unsafe expression.

_Example_
```
unsafe fn foo() {}
```
when called from another unsafe function, it must still be explicilty be called in an unsafe context
```
unsafe fn bar() {
    // foo(); // error: call to unsafe function outside of an unsafe expression

    // This however is fine
    unsafe foo(); 
}
```

## Const functions [↵](#functions)

A function may be declared as `const`, allowing the function to be called within a compile-time expression.

> _Example_
> ```
> fn runtime_foo() -> i32 { 42 }
> const fn const_foo() -> i32 { 42 }
> ```
> When using the first function in a place where a compile-time expression is expected, will result in a compiler error, i.e.
> ```
> const FOO = runtime_foo(); // error: `runtime_foo` cannot be called at compile-time
> ```
> however, using a const function allows this
> ```
> const FOO = runtime_foo(); // works fine
> ```

In additon, a function may be declared as `const!`, meaming that it will only be available to be called from a compile-time expression.

> _Example_
> ```
> const fn const_foo() -> i32 { 42 }
> const! fn comptime_foo() -> i32 { 42 }
> ```
> The first function may be called within runtime code
> ```
> fn bar() -> i32 {
>     const_foo() // works fine
> }
> ```
> But the latter will result in a compiler error
> ```
> fn bar() -> i32 {
>     comptime_foo() // error: `comptime_foo` is `const!`, and can therefore only be called at compile-time
> }
> ```

Const function cannot be `async`

## Methods [↵](#functions)
```
<method>           := <fn-specifiers> 'fn' <fn-receiver> <ext-name> <fn-signature> { <contract> }* <fn-body>
<fn-receiver>      := '(' ( <fn-self-reciever> | <fn-type-reciever> ) ')'
<fn-self-receiver> := [ '&' ] [ 'mut' ] 'self'
<fn-type-receiver> := [ 'mut' ] 'self' ':' <type>
```

A method is a function that is associated to type, and must be called on its type.
Methods are generally called directly on the type implementing the method, but can be called as a free function, if the first parameter has a `self` label with the corresponding type.

Methods take in a receiver that defines how the function will interact with its calling type.
The receiver is separate from any of its parameters, as this is a special deduced runtime parameter.
It also happens to help as a visual aid to quickly distinguish methods from associated functions.

The receiver may either be
- a `self` receiver, or
- a typed reciever

A self receiver takes on the type of the function the method is implemented on, and its syntax determines how the function takes in the value.
This is a shorthand for the following receivers:

`self` receiver | type receiver
----------------|-------------------
`self`          | `self: Self`
`&self`         | `self: &Self`
`mut self`      | `mut self: Self`
`&mut self`     | `self: &mut Self`

A typed receiver can take in a user specified reciever type, which may be one of the following
- `Self`
- `&Self` or `&mut Self`
- any type `T` using `Self` (i.e. `Self` must appear in the type), which implements `Deref(.Target = T)`

> _Todo_: `T` might need some kind of `Dispatch` trait associated with it instead of `Deref`

## Trait functions & methods [↵](#functions)
``` 
<trait-fn>            := <trait-fn-qualifiers> 'fn' <name> [ '?' ] <fn-signature> { <contracts> }* <trait-fn-body>
<trait-method>        := <trait-fn-qualifiers> 'fn' <fn-reciever> <name> [ '?' ] <fn-signature> { <contracts> }* <trait-fn-body>
<trait-fn-body>       := ';'
                       | <fn-body>
<trait-fn-qualifiers> := { <attribute> }* [ ( [ ( 'const'  [ '!' ] ) | 'async' ] [ 'unsafe' ] [ 'gen' ] ) ]
``` 

Trait functions and methods are special variants for their normal counterparts, which are used to define the signatures that any trait impl must implement.

If either is defined with a body, this will be their default implementation.
This allows the implementation not to provide an explicit implementation, in which case, the default implementation will be used.

They may provide a set of contracts, which need to be adhered to by its implementations.

In addition, there is also support for optional trait functions and methods, which are indicated with a `?` following the name.
These are function that are not required to be implementation and may not exists, requiring explicit handling to be called.
Calling these can be done using an [optional chaining call] or [optional chaining method call].

## Extern & exported functions [↵](#functions)

Functions can be declared as `extern` or `export`, allow interactions with external code.

By default, they use the `C` calling convention.
The calling convention can be explicitly set using the [`@callconv` attribute]

> _Note_: For more information, check the [extern & export blocks](./external-export-block.md) section.

### Extern [↵](#extern--exported-functions-)
```
<extern-fn-item>       := <extern-fn-qualifiers> 'extern' <string-literal> 'fn' <name> '(' <extern-fn-params> [ ',' [ <c-var-args> ] ] ')' [ <fn-return> ] ';'
<extern-fn-block-item> := <extern-fn-qualifiers> 'extern' 'fn' <name> '(' <fn-param> [ ',' <c-var-args> ] ')' [ <fn-return> ] ';'
<extern-fn-qualifiers> := { <attribute> }* [ <vis> ] [ 'safe' ]
<extern-fn-params>     := <extern-fn-param> { ',' <extern-fn-params> }* [ ',' ]
<extern-fn-param>      := <ext-name> ':' <type>
```

An `extern` function allows code to call a function that is declared in an external library that is not imported using a [`use` item].

`extern` functions are declared with the name of the external library the symbol can be found in, cannot have a body, and are `unsafe` by default.

When not declared inside of an [extern block], the `extern` function must explicitly provide the name of the library in which it is located, otherwise it will use that of the extern block.
The compiler will automatically add a prefix and an extension to these names, which depend on the OS being compiled to.
These can be controlled via arguments provided to the compiler.

`extern` functions may also explicitly be marked as `safe`, meaning that the author guarantees the safety of this function.

The parameters for an extern function define the labels uses to call the functions.

#### C variadic parameters [↵](#extern-)
```
<c-var-args> := '...'
```
External function also support C variadic parameters.

Whenever a functions uses these, the function cannot be made `safe`.

### Export [↵](#extern--exported-functions-)
```
<export-fn-item>       := <extern-fn-qualifiers> 'export' 'fn' <name> '(' <fn-params>  [ ',' ] ')' [ <fn-return> ] <fn-body>
<export-fn-block-item> := { <attribute> }* 'fn' <name> '(' <fn-param> ')' [ <fn-return> ] <fn-body>
<extern-fn-params>     := <extern-fn-param> { ',' <extern-fn-params> }* [ ',' ]
```

`export` functions define a function which can be imported and called by external code.

They cannot be accessed from any code importing the function using a [`use` item].

All `export` functions are always publically visible (`pub`).

Function labels only matter when the function is called when imported via a [`use` item].

## Generator functions [↵](#functions)

A function can be declared `gen`, indicating that the function is generator function.
Genetor functions are a way of programatically writing iterator-like types that can return a set of values.

The function can return multiple values with the use of [`yield`] expressions, and will continue to be able to yield even more values, until either a return or the end of the function is encountered.

## Async functions [↵](#functions)

A function can be marked as `async`, which allows it to be called from an `async` context.

They work differently to normal functions, as they are not directly executed.
Instead, they capture their arguments into a future, which can then be pooled to execute the actual code within the function.

Async function may also be declared as `unsafe`.

An async function is syntactic sugar for function with a very specific signature.
This is roughly equivalent to a function returning a `Future`, and having an `async` block in its body which it returns

> _Example_
> ```
> async fn example(x: &str) -> usize {
>     x.len()
> }
> ```
> would be equivalent to
> ```
> fn example(x: &str) -> impl Future(.Output = usize) {
>     async move {
>         x.len()
>     }
> }
> ```

Although in practice, this is more complex.
- the return type needs to keep track of each argument's lifetime
- the `async` block captures all parameters, even unused ones, to ensure the correct drop order
- the async function may store values on the stack, so additional management needs to be done

> _Todo_: The exact underlying abstraction used for async functions has not been figured out fully, as it would be preferable to be able to call these as if they are regular function,
>         to avoid any complication cause by function coloring.

### Async generator functions [↵](#async-functions-)

Async generator functions combine the async behavior of an async function with the [`yield`] behavior of a generator function.
Each call to yield, will in addition to producing a value, also act similarly to encountering an `async` call.

## Label-based psuedo-overloading [↵](#functions)

Since labels are part of the identifier of a function, they can be used to provide a kind of pseudo-overloading of functions.
The labels are then used to distinguish between different functions with the same function name.

Functions are checked for any comflicting identifiers at compile-time, regardless of whether they are called or not.

To do this, each identifier is converted to all possible variants that can be used to call the function.
The variants created depends on all optional and variadic parameters, create one per possible variation of provided labels.

> _Example_
> The function `foo(a: i32, b: i32 = 1, c: i32...)`, with the identifier `foo(a:?b:c...)` would have the following variants
> - `foo(a:)`
> - `foo(a:b)`
> - `foo(a:c)`
> - `foo(a:b:c)`

Each variant can then be compared for any possible collisions, if one is found, it will result in a compile error

### Collision examples

1) Different names: **_no collisions_**
   ```
   fn foo() {} // foo()
   fn bar() {} // bar()
   ```

2) Same name, no parameters: **_collision_**
   ```
   fn foo() {} // foo()
   fn foo() {} // foo()
   ```

3) Same name, identical parameters: **_collision_**
   ```
   fn foo(a: i32) {} // foo(a:)
   fn foo(a: f32) {} // foo(a:)
   ```

4) Overlap between required parameters: **_collision_**
   ```
   fn foo(a: i32, b: i32 = 1) {} // foo(a:b?:) -> foo(a)
   fn foo(a: f32, b: f32 = 1.0) {} // foo(a:?b:) -> foo(a)
   ```

5) Overlap between required and optional parameter: **_collision_**
   ```
   fn foo(a: i32, b: i32) {} // foo(a:b:) -> foo(a:b:)
   fn foo(a: f32, b: f32 = 0.0) {} // foo(a:?b:) -> foo(a:b:)
   ```

6) 2 matching parameters, but one has an additional required parameter: **_no collisions_**
   ```
   fn foo(a: i32, b: i32, c: i32) {} // foo(a:b:c:)
   fn foo(a: i32, b: i32 = 2) {} // foo(a:?b:)
   ```

7) Different order of parameters: **_no collisions_**
   ```
   fn foo(a: i32, b: i32) {} // foo(a:b)
   fn foo(b: i32, a: i32) {} // foo(b:a)
   ```

## String template functions [↵](#functions)
```
<template-string-fn> := <fn-qualifiers> 'template' 'fn' <fn-signature> { <contract> }* <fn-body>
```

A template string function is a special function which can be used by a [template string expression].

It takes in an interpolated string, and optionally a set of variadic arguments, which can be used to pass values without interpolcate them within the string itself.
And returns a custom type, this can be thought of like a [literal operator], but is called at runtime and can take the above mentioned interpolated string.
A common usecase of this is to map them to a `{N}` occurance within the string, and insert them there.

## Partial application (currying) [↵](#functions)

Function can be partially applied (also known as function currying).

A function containing constant arguments will, for example, be partially applied at compile time, based on those arguments.



[unsafe context]:                #unsafe-functions- "Todo"
[unsafe expression]:             #unsafe-functions- "Todo"
[`use` item]:                    ./use.md
[extern & export blocks]:        ./external-export-block.md
[extern block]:                  ./external-export-block.md#extern- "Todo: Section does not exist yet"
[`@callconv` attribute]:         ../attributes/abi-link-symbol-ffi.md#callconv-
[block expression]:              ../expressions/block-expressions.md
[constructing expressions]:      ../expressions/constructing-expressions.md
[implicit return]:               ../expressions/block-expressions.md#implicit-return- "Todo: Section does not exists yet"
[autoclosure]:                   ../expressions/closure-expressions.md#autoclosures-
[call expression]:               ../expressions/call-expressions.md#variadic-arguments- "Todo: Section does not exists yet"
[optional chaining call]:        ../expressions/call-expressions.md#optional-chaining-  "Todo: Section does not exists yet"
[literal expression]:            ../expressions/literal-expressions.md
[optional chaining method call]: ../expressions/method-expressions.md#optional-chaining-  "Todo: Section does not exists yet"
[path expressions]:              ../expressions/path-expressions.md
[parenthesized expressions]:     ../expressions/paren-expressions.md
[`return`]:                      ../expressions/return-expressions.md
[template string expression]:    ../expressions/template-string-expressions.md
[`yield`]:                       ../expressions/yield-expressions.md
[nested item]:                   ../statements/item-statements.md
[named tuple]:                   ../type-system/types/composite-types/tuple-types.md#named-tuples-
[function type]:                 ../type-system/types/function-like-types/function-types.md
[raw function type]:             ../type-system/types/function-like-types/raw-function-types.md#raw-function-representing-trait-types
[impl trait type]:               ../type-system/types/trait-types/impl-trait-types.md
[unit type]:                     ../type-system/types/unit-type.md