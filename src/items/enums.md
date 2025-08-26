# Enums
```
<enum-item>   := { <attribute> }* [ <vis> ] ( <adt-enum> | <record-enum> | <flag-enum> )
<adt-enum>    := [ 'mut' ] 'enum' <name> [ <generic-params> ] [ <where-clause> ] '{' <enum-members> '}'
<record-enum> := 'record' 'enum' <name> [ <generic-params> ] [ <where-clause> ] '{' <record-enum-members> '}'
```

An enum items is syntactic sugar to more easily define a named [enum type](#11119-enum-types-).

An enum's visibility defines only the visibility of the enum, and not any of its fields.

Meanwhile, an enum's mutability will be propagated as the mutability of the enum.

An enum declaration without any generic arguments, like:
```
enum name {
    // ...
}
```
Is equal to the following:
```
type name = enum {
    // ...
};
```

> _Todo_: Generics


## Flag enum [↵](#77-enum)
```
<flag-enum-item> := 'flag' 'enum' <name> '{' <flag-enum-members> '}'
```

A flag enum item is similar to the one above, and declares a [flag enum type](#flag-enum-types-).

A flag enum meanwhile cannot be declared with generics
```
flag enum {
    // ...
}
```
Is equal to the following
```
type name = flag enum {
    // ...
}
```
