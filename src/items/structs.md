# Structs
```
<struct-item> := { <attribute> }* [ <vis> ] ( <struct> | <tuple-struct> | <unit-struct> )
```

A structure item is syntactic sugar to be able to more easily define a structure type.
This can either be a structure or tuple structure type, depending on the syntax.

A structure's visibility defines only the visibility of the structure, and not any of its fields.

Meanwhile, a structure's mutability will be propagated as the mutability of the structure.

## Regular structs [↵](#structs)
```
<struct> := [ 'mut' | 'record' ] 'struct' [ <generic-params> ] [ <where-clause> ] '{' { <struct-elements> } '}'
```

A struct item generates a named [struct type].
Its contents are declared in the same way as a struct type.

The following struct declaration:
```
struct Foo {
    // ...
}

struct Bar(T: type, N: usize) {
    // ...
}
```
Is equal to the following:
```
type Foo = struct {
    // ...
};

type Bar = struct[T: type, N: usize] {
    // ...
};
```

## Tuple structure [↵](#structs)
```
<tuple-struct>      := [ 'mut' | 'record' ] 'struct' [ <generic-params> ] [ <where-clause> ] '(' <tuple-struct-fields> ')' <tuple-struct-body>
<tuple-struct-body> := ';'
                     | '{' { <assoc-item> }* '}'
```

A tuple struct item generates a named [tuple struct type].
Its content are declared the same way as a tuple struct type.

A tuple struct declaration without any generic arguments, like:
```
struct name(...);

struct name2(...) { ... }
```
Is equal to the following:
```
type name = struct (...);
type name2 = struct ( ... ) { ... };
```

> _Todo_: Generics

## Unit structs [↵](#structs)

```
<unit-struct>           := { <attribute> }* [ <vis> ] 'struct' <name> <unit-struct-decl-body>
<unit-struct-decl-body> := ';'
                         | '{' { <struct-element> }* '}'
```

A unit struct item declares a named [unit struct type].
Its declaration, like its related type, is identical to a regular struct, except that is contains no fields.

A unit struct declaration:
```
struct Unit;
struct Unit2 { ... }
```
Is equal to the following
```
type Unit = struct;
type Unit2 = struct { ... };
```


[struct type]:       ../type-system/types/struct-types.md
[unit struct type]:  ../type-system/types/struct-types.md#unit-structs-
[tuple struct type]: ../type-system/types/tuple-struct-types.md