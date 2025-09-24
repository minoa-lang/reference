# Enums
```
<enum-item> := { <attribute> }* [ <vis> ] ( <adt-enum> | <flag-enum> )
```

An enum item defines a named enum type.
Unlike a plain [enum type], the cannot be anonymous.
This is imilar to creating a distinct type alias to an anonymous enum, with some additional syntax features.

Enum items can declare the visibility for an enum type, which applies to all underlying variants and fields.

Enum items may declare either regular/ADT enums or flag enums.

## ADT enum
```
<adt-enum> := [ 'mut' | 'record' ] 'enum' <name> [ <generic-params> ] [ <discriminant> ] [ <where-clause> ] '{' <enum-members> '}'
```

An ADT enum generates a named [enum type].
Its contents are declared in the same way as defined in the enum type.

The `mut` specifier may be added to a enum type, indicating that all fields in the struct will be mutable.
Similarly, the `record` specifier may be added to make the enum into a record enum type.

> _Example_
> ```
> enum Foo : u8 {
>     A,
>     B(i32, i32),
>     C {
>         x: i32,
>         y: f32
>     }
> }
> ```

In addition, generic parameters and a where clause can be added to generate a generic enum.

> _Example_
> ```
> enum Foo<T> : u8 where T: Copy {
>     A,
>     B(T)
> }
> ```

## Flag enum
```
<flag-enum> := 'flag' 'enum' <name> [ <discriminant> ] '{' <flag-enum-members> '}'
```

A flag enum generates a name [flag enum type].
It's contents are declared in the same way as defined in the flag enum type.

> _Example_
> ```
> flag enum Foo : u8 {
>     A,
>     B,
> }
> ```



[enum type]:      ../type-system/types/composite-types/enum-types.md
[flag enum type]: ../type-system/types/composite-types/enum-types.md#flag-enum-types-