# Variable declarations
```
<var-decl> := <simple-var-decl> | <pattern-var-decl> | <uninit-var-decl>
```

A variable declaration allows for the introduction of one or more local variable within the current scope.

By default, variables are immutable, unless introduced with an explicit `mut`, in which case they can be mutated.

If no type is provided, teh compiler will infer the type from the surrouning context, if not enough information is available, it will result in an error.

Each variable introduced by a variable will live until the end of the current scope, and will be accessible until then, unless it is shadowed by another value.

> _Note_: It is generally recommeded to keep variables immutable, as it can allow for additional/better optimizations.

## Simple variable declaration [↵](#variable-declarations)
```
<simple-var-decl> := [ 'mut' ] <name> { ',' <name> }* ':' [ <type> ] '=' <expr> ';'
<var-name>        := <name> | '_`
```

Simple variable declarations introduce provide a quick way of introducing variables.
It directly assigned the result of an expression to one or more names.

> _Example_
> ```
> a := 1;
> b, c := 10, 20;
> ```

When the declaration is started using a `mut`, it applied to all values defined within the declaration.

> _Example_
> ```
> mut d := 10;
> d = 40;
> 
> mut e, f := 50, 60;
> e = 15;
> f = 16;
> ```

A type may optionally be provided, which will be applied to all values that are defined within the declaration, meaning that `a, b: T = ...;` will result in both `a` and `b` being of type `T`.

> _Example_
> ```
> a, b: i32 = 1, 2;
> ```
> is equivalent to
> ```
> a: i32 = 1;
> b: i32 = 2;
> ```

When more than 1 name is provided, the expresion must be one of the following:
- a [comma expression]
- expression returning any of the following types (with the matching number of fields):
  - [tuple type]
  - [tuple struct type]

If the latter is provided, the value will be automatically destructured.

Additionally, in this case, the use of an `_` will discard any of the values.

> _Example_
> ```
> a, _ := (1, 2);
> ```
> is equivalent to
> ```
> a = 1;
> ```
> since the value of `2` is discarded by the fact that it is assigned to an underscore

## Pattern variable declaration [↵](#variable-declarations)
```
<pattern-var-decl>      := 'let' [ 'mut' ] <pattern-top-no-alt> [ ':' <type> ] '=' <expr> [ 'else' <block-expr> ]
<pattern-var-decl-expr> := <expr> ';'
                         | ? <expr>, except any expression supporting an 'else' ? 'else' <expr> ';'
```

A pattern variable declaration, also knows as a let bindings, allows local variables to be declared directly when destructuring a value.

> _Example_: Irrefutable pattern
> ```
> struct Foo { a: i32, b: [3]i32 }
> 
> f := Foo { a: 1, b: [2, 3, 4] };
> 
> let .{ a, b: [ c, .. ] } = f;
> ```

If a refutable pattern is provided, the declaration must contain an `else` with an expression, which may be on of the following:
- an expression returing values for each identifier pattern in the pattern, in the order they are defined within the pattern
- a block which contains code which terminates function execution, e.g. return or panic

> _Example_
> ```
> enum Foo {
>     A(i32),
>     B(f64),
>     C {
>         a: i32,
>         b: [3]f32,
>         c: i32
>     }
> }
> 
> f := Foo.A(2);
> 
> // Will return the value stored in `B` if it matches, and will otherwise return 2.0
> let Foo.B(val) = f else 2.0;
> 
> // Early return if the pattern does not match
> let Foo.A(val) = f else { return; }
> 
> 
> bar := Bar { a: 42, b: [0, 1, 2], c: 3.14 };
> 
> let Foo.C { a, b: [ d, _ e], c } = f else (1, 2.0, 3.0, 4);
> ```

## Uninit variable declaration [↵](#variable-declarations)
```
<uninit-var-decl> := 'let?' [ 'mut' ] <name> { ',' <name> }* ':' <type> ';'
```

An uninit variable declaration allows for a set of variables to be declared without an initial value.
These are expected to be assigned later on in the function.

Even if these variables are not mutable, they can have their initial value still assigned somewhere later in the same scope.

To be able to use any of these variables, the compiler must be able to ensure that the variable will be assigned a value at the moment of use.

> _Example_
> ```
> let? a, b: i32;
> 
> if foo() {
>     a = 10;
> } else {
>     a = 8;
> }
> 
> // error: cannot assign a value to an immutable value after its initialization
> // a = 20;
> 
> 
> if a == 10 {
>     b = 2
> }
> 
> // error: `b` is not initialized in all codepaths
> // bar(b);
> ```

## Unwrapping variable declaration [↵](#variable-declarations)
```
<unwrap-var-decl> := 'let!' <name> [ ':' <type> ] '=' <expr> ';'
```

An unwrap variable declaration is a special declaration which allows the unwrapping of a value without incurring the cost of it.
This is limited to [option] and [result] types.

Because of this, the compiler needs to be able to determine that it is safe to unwrap it, i.e. a check based on the value happened earlier in code.

> _Example_
> ```
> fn determine_based_on_controlflow(opt: ?i32) {
>     if opt == null {
>         // some processing
>         return;
>     }
> 
>     // The compiler can determine that the `opt` value cannot be null, as the function would have been exited otherwise
>     let! val = opt;
> }
> 
> fn determine_based_on_assign(mut opt: ?i32) {
> 
>     opt = 2;
> 
>     // `opt` explicitly set to a valid value earlier in the function
>     let! val = opt;
> }
> ```

[comma expression]:  ../expressions/comma-expressions.md
[tuple type]:        ../type-system/types/composite-types/tuple-types.md
[tuple struct type]: ../type-system/types/composite-types/tuple-struct-types.md
[option]:            ../type-system/types/abstract-types/optional-types.md
[result]:            ../type-system/types/abstract-types/result-types.md