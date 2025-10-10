# Consts
```
<const-item> := { <attribute> }* [ <vis> ] 'const' <name> [ ':' <type> ] '=' <expr> ';'
```

A constant items rperesents a named constant value which is not asssociated with any location in memory, i.e. the value solely exists at compile time.

Whenever a constant item is used, it is essentially inlined at the location it is used at, meaning it will be directly copied into the location it's being used in.
This also applies to non-`Copy` items.
When a reference is taken to a constant value, there is no guarantee that the resulting reference will correspond to any other reference of the constant value..

Constant values are valid throughout the entirety of an program's lifespan, including all references to a constant value.

Constants are generally declared with an explicit type, but this may be left out in cases where it is possible to derive the type from the expression.
The constant's type can only inferred if there can be no confusing about the type of the initializer expresion.

> _Example_: the literal `5` may be any integer type, and therefore the constant cannot infer its correect type, but the literal expression `5i32` can.

A reference to a constant is eligable for [promotion] to a constant value.

Whenever a constant value has a [pointer-like type], it may not be mutable.

## Constants with destructors

The type of a constant value may have a destructor.
This will be called whenever any copy of the constant value goes out of scope.

> _Example_
> ```
> struct DropType(i32) {
>     impl as Drop {
>         fn(&mut self) drop() {
>             print("Dropped with a value of \{self}")
>         }
>     }
> }
> 
> const CONST_WITH_DESTRUCTOR = DropType(3);
> 
> fn create_and_drop_constant_with_destructor() {
>     value := CONST_WITH_DESTRUCTOR;
>     // `value` will be dropped at the end of the function, calling `value.drop()`
>     // prints "Dropped with a value of 3"
> }
> ```

## trait constant [↵](#const-item)
```
<trait-const> := 'const' <name> ':' <type> [ '=' <expr> ] ';'
```

A trait constant declares a constant that is associated with that trait's implementation.

The default value may be provided, which will be used when no explicit type alias is defined within an implemention.


[const functions]:     ./functions.md#const-functions-
[block expressions]:   ../expressions/block-expressions.md
[calls]:               ../expressions/call-expressions.md
[literal expression]:  ../expressions/literal-expressions.md
[operator expression]: ../expressions/operator-expressions.md
[promotion]:           ../type-system/destructors.md#constant-promotion-
[pointer-like type]:   ../type-system/types/pointer-like-types.md