# Structs
```
<struct-item> := { <attribute> }* [ <vis> ] ( <struct> | <tuple-struct> | <unit-struct> )
```

A structure item defines a named structure type, unlike a plain [struct type], they cannot be anonymous.
This is similar of creating a distinct type alias to an anonymous struct, with some additional syntax features.

Struct items may declare either regular structures, tuple structures, or unit structures.
These are distinguished between by the syntax used.

Similarly to a struct type, a `mut` specifier may be added to a struct type, indicating that all fields in the struct will be mutable.

In addition, struct items may also define a default visibility for all fields and associated items in the struct before the struct item itself.

## Regular structs [↵](#structs)
```
<struct> := [ 'mut' | 'record' ] 'struct' [ <generic-params> ] [ <where-clause> ] '{' { <struct-elements> } '}'
```

A struct generates a named [struct type].
Its contents are declared in the same way as defined in the struct type.

> _Example_:
> ```
> struct Foo {
>     a: i32,
>     b: i32,
> 
>     fn foo() {}
> }
> ```

In addition, generic parameters and a where clause can be added to generate a generic structure.

> _Example_
> ```
> struct Foo[T] where T: Copy {
>     t: T,
> 
>     fn foo(val: T) {}
> }
> ```

## Tuple structs [↵](#structs)
```
<tuple-struct>      := [ 'mut' | 'record' ] 'struct' [ <generic-params> ] [ <where-clause> ] '(' <tuple-struct-fields> ')' <tuple-struct-body>
<tuple-struct-body> := ';'
                     | '{' { <assoc-item> }* '}'
```

A tuple struct generates a named [tuple struct type].
Its contents are declared in the same way as defined in the tuple struct type.

> _Example_:
> ```
> struct Foo (i32, i32) {
>     fn foo() {}
> }
> ```

In addition, generic parameters and a where clause can be added to generate a generic structure.

> _Example_
> ```
> struct Foo[T](T) where T: Copy {
>     fn foo(val: T) {}
> }
> ```

## Unit structs [↵](#structs)
```
<unit-struct> := 'struct' <name> ';'
```

A unit struct is a special struct that has no fields and may be constructed by just the struct's name.
This is the only way of defining a unit struct.

The following restrictions are applied to a unit struct:
- they may not contain any initializer items
- any associated items must be defined within a separate [implementation item]

> _Example_:
> ```
> struct Unit;
> 
> // We can only implement items on it externally
> impl Unit {
>     fn foo() {}
> }
> ```



[implementation item]: ./implementations.md
[struct type]:         ../type-system/types/struct-types.md
[unit struct type]:    ../type-system/types/struct-types.md#unit-structs-
[tuple struct type]:   ../type-system/types/tuple-struct-types.md