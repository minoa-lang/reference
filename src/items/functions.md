# Functions
```
<fn-item>       := <fn-qualifiers> 'fn' <name> [ <deduced-params> ] '(' [ <fn-params> ] ')' [ <fn-return> ] [ <where-clause> ] { <contract> }* <block>
<fn-qualifiers> := { <attribute>* } [ <vis> ] [ 'const' | 'async' | 'lazy' ] [ 'unsafe' ]
```

A function consists of a named block of code, together with a set of parameters (implicit and explicit), and a return type, representing a unit of reusable code.

When refered to, a function yields the corresponding zero-sized [function type], when when called evaluates to a direct function call.

A function may be declared `unsafe`, making it's entire body an unsafe context, but requires the function itself to be called from an unsafe context.

Each function has an identifier used to look up the function.
A function identifier is based on the actual function name + the names of the labels of the required and optional parameters.
This is done by having taking the function name, and following that with a list of `:`-separated label names, surrounded by parentheses.
These is always a trailing `:`.
Optional paramters are prefixed with a `?`.
If a variadic paramter exists, the label will be followed by `...`
For example, the function `foo(a: i32, b: i32 = 1, c...: i32)` will have the identifier `foo(a:?b:...)`.

Some examples of this:
```
fn foo() {} // -> foo()
fn foo(a: i32, b: i32) {} // -> foo(a:b:)
fn foo(:_ a: i32, :_ b: i32) {} // -> foo(_:b:)
fn foo(a: i32, :_ b: i32, :_ c: i32, d: i32) {} // -> foo(a:_:_:b:)

// With optionals
fn foo(a: i32 = 2) {} // -> foo(?a:)
fn foo(a: i32, b: i32 = 3) {} // -> foo(a:?b:)
```
> _Todo_: Effects

## Parameters [↵](#functions)
```
<fn-params>      := <fn-param> { ',' <fn-param> }* [ ',' ]
                  | [ <fn-req-params> ',' ] <fn-opt-params> [ ',' ]
                  | [ <fn-req-params> ',' ] [ <fn-opt-params> ',' ] <fn-variadic-params>

<fn-req-params>  := <fn-param> { ',' <fn-param> }*
<fn-param>       := { <attribute> }* <fn-param-name> ':' <type>

<fn-param-name>  := [ <fn-param-label> ] [ 'mut' | 'const' ] <name> { ',' [ <fn-param-label> ] [ 'mut' | 'const' ] <name> }
                  | [ <fn-param-label> ] [ 'const' ] <pattern-top-no-alt>
```

A function parameter represents one or more values that can be passed to a function.
Each parameter can be specified by either
- a collection of one of more comma-separated names, each with a mutability specified, or
- a pattern

If multiple names are provided, the parameter will be split into multiple invidual paramters.
Otherwise, if a  pattern is used within a parameter, it must be an [irrifutable pattern], for example:
```
fn name((value, _): (i32, i32)) -> i32 { value }
```

Parameter may be passed either as:
- `mut`: meaning that the value passed via the parameter may change, or
- `const`: meaning that the parameter is a compile-time parameter.

If a pattern is used, the user may apply a `const` modifier to the entire pattern.
The programmer must then ensure that no mutable bindings appear withing the pattern.

Any parameter of type `type` must be a constant parameter.

Any 2 parameters may not share a label.

> _Note_: In addition to strong keywords, the pattern used for a parameter may not include the weak keyword `lazy`

### Labels [↵](#731-parameters-)
```
<fn-param-label> := <name>
```
Labels provide an explicit name to a given argument, which is then used when calling the function.
These calls require labels to be provided to be able to select the correct version of a function

If an explicit label is provided, it can either be:
- a name, this will then be the name will be the label bound to the given parameter and must be used to pass data along the the function, or
- an `_`, this implies a label-less parameter, meaning that no name needs to be provided when calling a function

If no explicit label is provided, a label is picked depending on the parameter:
- for every name, the label will be called the same, e.g. `foo: i32` will become `foo foo : i32`
- for every pattern, the parameter will be a label-less parameter.

Argument labels are also allowed to take on the name of keywords, with the exception of the `self` keywords, as it is a special label.
For example: `fn find(struct value: Foo, in container: Bar)`.

> _Note_: A label of `self` is not allowed

> _Note_: Labels don't need an explicit syntax to be introduced, as 2 identifier following each other can never be a pattern, so it will always be a label & pattern

> _Todo_: Should labels be allowed to be keywords? as label are clearly marked

### Optional parameters [↵](#parameters-)
```
<fn-opt-params> := <fn-opt-param> { ',' <fn-opt-param> }
<fn-opt-param>  := <fn-param> '=' <expr>
```

A parameter may be provided with a default value for when no explicit value is given when calling the function, these are known as optional parameters.
Optional parameters must appear after all non-optional (also known as required) parameters.
A default value needs to a be a value that is known at compile-time.

When calling a function with optional parameters, each argument may appear in any order relative to other optional arguments, but they must appear after any required arguments.

An optional parameter may not be label-less, i.e. it may not have `:_` as a label.

### Variadic parameters [↵](#parameters-)
```
<variadic-param>  := <var-param-names> '...' ':' <var-param-types> [ '=' <expr> { ',' <expr> } [ ',' ] ]
<var-param-names> := <name> { ',' <name> }*
<var-param-types> := <type> { ',' <type> }*
```

A variadic parameter allows for 0 or more instances of its parameter set to be passed to a function.
They therefore allow for an unknown amount of parameters to be passed to a function/method.

A parameter set is the collection of names/types provided to the variadic parameter.
Therefore, the number of names and types provided must match.

When providing values, for parameter set, all elements need to be passed, meaning that the total amount of arguments passed as variadic arguments needs to be an integer multiple of the number of names defined within the variadic parameter's definition.

If a default value is provided an no values are provided at the call site, the variadic parameter will have the default value.

### Deduced parameters [↵](#parameters-)
```
<deduced-params> := '[' <deduced-param> { ',' <deduced-param> } ']'
<deduced-param>  := <name> ':' <type>
```

A deduced parameter is a parameter that can be deduces from the arguments passed into the function.
All deduced parameters are automatically compile-time values.

Example
```
// Both `T` and `N` can be inferred from the type of `arr`
fn foo[T: type, N: usize](arr: [N]T) { ... }
```

If a deduced parameter has not enough information from the rest of the function, an compiler error will be emitted.
For example: 
```
fn foo[T: type, U: type](val: &T) -> U { ... } // error: cannot decude the type of `U` from the signature
```

### Lazy parameters [↵](#parameters-)

Lazy parameters allow for late execution of a parameters, or even avoid execution when this parameter is not utilized.
But unlike closure arguments, lazy parameters allow the compiler to lift additional code in the surrounding context into the closure for the lazy variable.
This is limited to any code which only the lazy argument is relying on.

> _Note_: These are essentially syntactic sugar for closures taking in [autoclosure] arguments

> _Note_: Lazy parameters do introduce a deduced parameter into the function for the closure.

## Return [↵](#functions)
```
<fn-return> := '->' [ <name> ':' ] <type>
```
A function return may define a return in 2 ways:
- using a named return, allowing the name to be assigned within the function, this is the value that will be returned when no explicit return is called.
  Meaning that this may still be overwritten explicitly.
- using an unnamed type, meaning that a value must be expliclitly returned from a function.

A special case is when the return type is a named tuple with all fields named. In this case, the individual fields may be assigned directly like a names return would.

If a named return exists and no explicit return is used, the named return or the named tuple fields must be assigned a value.

When no return is explicitly stated, an implicit [unit type] will be the return type.

## Function body [↵](#functions)

A function body is the part of the function that contain the actual code that is run when calling the function.
Within a function body, the programmer has access to all parameters and named return values.

When a parameter uses a pattern, the body has access the the bindings within the pattern.

These act like a [block expression], allowing both explicit returns, and returns using the last expression within the block.

## Const functions [↵](#functions)

A const function is a version of a function which may be called either at compile time or at runtime.

Functions returning a [`type` type] are restricted to a compile time context.

## Methods [↵](#functions)
```
<method>             := <method-qualifiers> 'fn' '(' <receiver> ')' <name> [ <implicit-params> ] '(' [ <fn-params> ] ')' [ <fn-return> ] [ <where-clause> ] { <contract> }* <block>
<method-qualifiers>  := { <attribute> }* [ 'const' ] [ 'unsafe' ]
<reciever>           := [ '&' ] [ 'mut' ] 'self'
                      | 'self' ':' <type>
```

A method is a special type of associated function which also takes in a receiver, allows a function to be called directly on a value of a given type.
Methocs need to be associated with a type, there are also special versions of a method for traits and constraints.

A methods receiver is located seperately from the parameters, as it is a special inferred parameter, which works differently than other inferred parameters.
The receiver, unlike inferred parameters is not a compile-time parameter, but a runtime parameter. 
It also helps as a visual aid to quickly distinguish methods.

A receiver may have one of the following types:
- `Self`
- `&Self`
- `&mut Self`
- any `T` that implements `Deref(.Target = Self)`

> _Todo_: `T` might need some kind of `Dispatch` trait associated with it instead of `Deref`

The receiver may also be written in shorthand, which results in the following full receivers:
shorthand  | full
-----------|------------------
`self`     | `self: Self`
`&self`    | `self: &Self`
`&mut self`| `self: &mut Self`

When the `self` shorthand is used, `mut` may be added to make the value mutable.

When a method is called as if it were a function, the receiver can be passed as a parameter with the `self` label.

## Trait functions & methods [↵](#functions)
```
<trait-fn>            := <trait-fn-qualifiers> 'fn' <name> [ '?' ] [ <deduced-params> ] '(' [ <fn-params> ] ')' [ <fn-return> ] [ <where-clause> ] { <contract> }* <trait-fn-body>
<trait-method>        := <trait-fn-qualifiers> 'fn' '(' <receiver> ')' <name> [ '?' ] [ <deduced-params> ] '(' [ <fn-params> ] ')' [ <fn-return> ] [ <where-clause> ] { <contract> }* <trait-fn-body>
<trait-fn-qualifiers> := { <attribute> }* [ 'const' | 'async' ] [ 'unsafe' ]
<trait-fn-body>       := ';' | <block>
```

Trait functions and methods are similar to their normal counterparts, but define a signature which a function or methods in a trait impl needs to implement.

They can be provided with a default implementation, if one is provided, they are not required to be implemeneted within their trait impl, but may be overriden with a specialized implementation.

If a trait function or method has a set of contracts, any implementation needs to adhere to the given contracts.

In addition, trait support so-called optional functions/methods, these are defined by having a `?` following the name of the function.
This does not require the function/method to have an implementation on the implementing type, allowing it to only be supplied when neccesary for any use to correctly function.
These functions/methods need to be called using the 

## External & exported functions [↵](#functions)
```
<extern-fn-item>       := { <attribute> } [ 'safe' ] 'extern' <string-literal> 'fn' <name> '(' <extern-fn-params> ')' [ '->' <type> ] ';'
<extern-block-fn-item> := { <attribute> } [ 'safe' ] 'fn' <name> '(' <extern-fn-params> ')' [ '->' <type> ] ';'

<export-fn-item>       := { <attribute> } 'export' fn <name> '(' <extern-fn-params> ')' [ '->' <type> ] <block>
<export-block-fn-item> := { <attribute> } 'fn' <name> '(' <extern-fn-params> ')' [ '->' <type> ] <block>
```
d
Functions can be declared as `extern` or `export`, allowing interaction with external code.
If a function is `extern`, it cannot have a body, meanwhile `export` functions must have a body.

`extern` functions are declared with the name of the external library the symbol is found it.
The compiler will automatically add a prefix and an extension to these names.
The prefix and extension will depend on the OS being compiled for.
Individual function may be explicitly marked as safe, if the safety can be guaranteed by the implementer

Both `extern` and `export` function will default to a `.C` calling convention.
The blocks may define the default calling convention using the [`@callconv` attribute].

> _Note_: For more information, check the [external & export blocks](./external-export-block.md) section.

> _Note_: To set a non-external function to use the `contextless` ABI, use the [`contextless`] attribute.

## Generator functions

A generator function is a function that contains one or more [`yield`]s.
It is allows for writing iterators as if they were normal functions, but can return multiple values.

A generator function must return an `impl Iterator` type.

A generator function will continue to produce values until its last `yield`, or a return is encountered.

## Async functions [↵](#functions)

An async function allows a function to be called in an async context.

They do not work like normal functions where they are just executed when called.
Instead they capture their arguments into a future, which can then be polled to execute the actual contents of the function.

An async function is syntactic sugar for a function with a very specific signature.
This is roughly equivalent to a function returning a `Future`, nad having an `async` block in it's body, i.e.
```
async fn example(x: &str) -> usize {
    x.len()
}
```
is equivalent to
```
fn example(x: &str) -> impl Future(.Output = usize) {
    async move {
        x.len()
    }
}
```

Although in practice, this is more complex:
- the return type needs to keep track of each argument's lifetime
- the `async` block captures all parameters, even unused ones, to ensure the correct drop order, as it would be a regular function.

> _Todo_: Async functions should be able to be called from outside of an async context, avoiding the need for colored function. The exact details are not yet figured out

### Async generator functions [↵](#async-functions-)

An async generator funcition, as its name implies, is a generator function declared as async, allowing it to use everything an async function can use.
Each yi

### Async unsafe functions [↵](#async-functions-)

An async function may also be declared to be unsafe.
The resulting function can therefore do whatever an unsafe function is allowed to.

While calling the function is unsafe, the future returned by the async function is not, and can therefore be awaited outside of an unsafe context.

## Label-based overloading [↵](#functions)

Support for function overloading is based on labels.
Since label names are required to be used, we can check collisions when processing the functions themselves and we don't need to worry about them when trying to resolve the symbol.

First, each function will be converted to a collection of possible variants which are checked.
Variants are produces by going over every possible combination of optional parameters, and creating an function identifier from them.
For example, `foo(a: i32 = 0, b: i32 = 1)` would have the following variants to be checked:
- `foo()`
- `foo(a)`
- `foo(b)`
- `foo(a:b)`

Then each variant will be compared to the variants of the function it is compared to.
If a collision exists, a compiler error will be emitted.

> _Note_: Since optional arguments may appear in any order, the function `foo(a: i32 = 0, b: i32 = 0)` will check for both if either the identifier `foo(?a:?b)` or `foo(?b:?a)` would collide

### Examples [↵](#label-based-overloading-)

1. Different names: **_no collision_**
```
fn foo() { ... } // foo()
fn bar() { ... } // bar()
```

2. Same number of paramters with same labels: **_collision_**
```
fn foo() { ... } // foo()
fn foo() { ... } // foo()
```
or
```
fn foo(a: i32) { ... } // foo(a:)
fn foo(a: f64) { ... } // foo(a:)
```

3. Overlap between func with required and func with default values: **_collision_**

(0) collides with (1), since (1) has the variant `foo(a:b)`
```
fn foo(a: i32, b: i32)     // (0) foo(a:b:)
fn foo(a: i32, b: i32 = 0) // (1) foo(a:?b:)
```
or (0) collides with (1), since (0) and (1) both have `foo(a)` and `foo(a:c)` as shared variants
```
fn foo(a: i32,             c: i32)     // (0) foo(a:?c:)
fn foo(a: f64, b: i32 = 0, c: i32 = 1) // (1) foo(a:?b:?c:)
```

4. Overlap between func with required and func with default values, but with a non-default left over: **_no collision_**

In both examples, (0) always needs to have `d` passed, but (1) has no variant with `d`

```
fn foo(a: i32, b: i32, d: i32) // (0) foo(a:b:d:)
fn foo(a: i32, b: i32 = 0)     // (1) foo(a:?b:)
```
or
```
fn foo(a: i32,             c: i32, d: i32) // (1) foo(a:c:d:)
fn foo(a: f64, b: i32 = 0, c: i32 = 1)     // (1) foo(a:?b:?c:)
```

5. Any left over defaults: **_collision_**

(0) collides with (1), since (1) has the variant `foo()`
```
fn foo()           // (0) foo()
fn foo(a: i32 = 0) // (1) foo(?a:)
```
or (0) collides with (1), since (0) and (1) both have `foo(a)` and `foo(a:c)` as shared variants
```
fn foo(a: i32, c: i32 = 0) // (0) foo(a:?c:)
fn foo(a: i32, c: i32 = 1) // (1) foo(a:?c:)
```

6. Both have variadics: **_collision_**

> _Todo_: Correct syntax + figure out new variadics

```
fn foo(a: i32, b: i32...) // foo(a:...)
fn foo(a: i32, c: f64...) // foo(a:...)
```
or 
```
fn foo(a: i32,             c: i32...) // foo(a:...)
fn foo(a: i32, b: i32 = 1, c: i32...) // foo(a:?b:...)
```

## Template string functions [↵](#functions)
```
<string-template-fn> := <fn-qualifiers> 'template' 'fn' <name> [ <deduced-params> ] '(' <name> ':' ? Interpolated stringtype ? [ ',' <variadic-params> ] ')' [ '->` <type> ]  [ <where-clause> ] { <contract> }* <block>
```

A string-template function is a special function which can be used by a [template string expression](#931-template-string-expression).

It takes in an interpolated string, and optionally a set of variadic arguments, which can be used to pass values without interpolating them within the string itself.
A common usecase of this is to map them to a `{N}` occurance within the string, and insert them there.


[`@callconv` attribute]:      #callconv
[external & export blocks]:   ./external-export-block.md
[`contextless`]:              ../attributes.md#contextless-
[block expression]:           ../expressions/block-expressions.md
[autoclosure]:                ../expressions/closure-expressions.md#autoclosures-
[template string expression]: ../expressions/template-string-expressions.md
[`yield`]:                    ../expressions/yield-expressions.md
[function type]:              ../type-system/types/function-types.md
[`type` type]:                ../type-system/types/type-type.md
[unit type]:                  ../type-system/types/unit-type.md
[irrifutable pattern]:        ../patterns.md