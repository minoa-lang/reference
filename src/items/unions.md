# Unions
```
<union-item> := { <attribute> }* [ <vis> ] [ 'mut' ] 'union' <name>  [ <generic-params> ] [ <where-clause> ] '{' <union-members> '}'
```
A union item is syntactic sugar to more easily define a named [union type].

A union's visibility defines only the visibility of the union, and not any of its fields.

Meanwhile, an enum's mutability will be propagated as the mutability of the union.

The following struct declaration:
```
union Foo {
    // ...
}

union Bar(T: type, N: usize) {
    // ...
}
```
Is equal to the following:
```
type Foo = union {
    // ...
};

type Bar = union[T: type, N: usize] {
    // ...
};
```

[union type]: ../type-system/types/union-types.md