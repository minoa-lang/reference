# Unions
```
<union-item> := { <attribute> }* <type-layout-specifiers> [ <vis> ] [ 'mut' ] 'union' <name>  [ <generic-params> ] [ <where-clause> ] '{' <union-members> '}'
```

A union item defines a named union type.
Unlike a plain [union type], the cannot be anonymous.
This is similar to creating a distinct type alias to an anonymous union, with some additional syntax features.

Similarly to a union type, a `mut` specifier may be added to a union type ,indicateing that all fields in the union will be mutable.

In additon, union items may also define the default visibility for all fields and associated items in the union before the union itself.

> _Example_
> ```
> union Foo {
>     i: i32,
>     f: f32,
> 
>     fn foo() {}
> }
> ```

In addition, generic parameters and a where clause can be added to the generated union

> _Example_
> ```
> union Foo[T] where T: Copy {
>     t: T,
>     i: i32,
> }
> ```

[union type]: ../type-system/types/composite-types/union-types.md