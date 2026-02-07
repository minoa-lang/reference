# Implementations

An implementation is an item which can associate other items with a given type.
Implementation may either be provided externally from the type which it implements items on, or within their type definition.

The type that is being implement on is called the _implementing type_, and the associated items are the _associated items_ of the implementing type.


## External implementations [↵](#implementations)
```
<impl-item> := <inherent-impl>
             | <trait-impl>
```

An external implementation is declared outside of the type definition of the type they are implemented on.

### Inherent impl [↵](#external-implementations-)
```
<inherent-impl> := { <attribute> }* [ <vis> ] [ 'unsafe' ] 'impl' [ ?no whitespace? <generic-params> ] ?whitespace? <type> [ <where-clause> ] '{' { <assoc-item> }* '}'
```

An inherent implementation is defined without any trait.

This allows items to be directly implemented on the type, and these can be directly called on the type or a value of the type.
The path for these items is that of the implmenting type, followed by the identifier of the associated item.

A type may have as many inherent implementation as it wants, with the only limitation is that each identifier may only appear once per type.

Inherent implementations are limited to the library defining the type.

All inherent implementations support a [visibility], this visibility will then be propagated to any associated item that does not have their visibility explicitly defined.
If the implementation is marked as `unsafe`, any functions and methods defined within the implementation will be `unsafe` functions and methods.

> _Example_
> ```
> pub mod color {
>     pub struct Color(pub u8, pub u8, pub u8);
> 
>     impl Color {
>         pub const WHITE: Color = Color(255, 255, 255);
>     }
> }
> 
> mod values {
>     use super.color.Color;
>     impl Color {
>         pub fn red() -> Color {
>             Color(255, 0, 0)
>         }
>     }
> }
> 
> pub use self.color.Color;
> fn main() {
>     // Axtual path to the implementing type and impl in the same module
>     color.Color.WHITE;
> 
>     // Impl blocks in different modules are still accessed through a path to the type
>     color.Color.red();
> 
>     // Re-exported paths to the implementing type work as well
>     Color.red();
> 
>     // Does not work, because the `use` in `values` is not public
>     values.Color.red();
> }
> ```

### Trait impl [↵](#external-implementations-)
```
<trait-impl> := { <attribute> }* [`const`] [ 'unsafe' ] [ 'extend' ] 'impl' [ ?no whitespace? <generic-params> ] ?whitespace?  <type> 'as' <trait-bound> [ <where-clause> ] '{' { <assoc-item> | <disambig-alias> }* '}'
```

A trait implementation is defined with a trait that the type will implement.
The trait is called the _implmenented trait_ of the implementing type.

A trait implementation must define all non-default non-optional associated items declared by the implemented type trait.
It can also redefine (i.e. override) an item's default implementation defined in the trait itself.
And it can implementing an optional function or method, the `?` after the name in the trait is left out, i.e. implementing `fn opt?()` as `fn opt()`.
The implementation is not allow to add any associated items that are not declared in the trait.

Unsafe traits require the `unsafe` keyword to be added to the implementation.

A trait implementation may also implement a trait as `const`, this means that any of the associated items in the trait implementation may be used in a compile-time context.

By default, any of the trait items cannot be accessed without using a trait disambiguation.
`extend` can be used to add these items directly within the namespace of the type.

> _Example_
> ```
> struct Circle {
>     radius: f64,
>     center: Point,
> }
> 
> impl Circle as Copy {}
> 
> impl Circle as Clone {
>     fn(&self) clone() -> Circle { *self };
> }
> 
> // Allow `draw` and `bounding_box` to be directly called on `Circle`, e.g. `c.draw()`
> extend impl Circle as Shape {
>     fn(&self) draw(s: Surface) { draw_cirlce(s, *self); }
>     fn(&self) bounding_box() -> BoundingBox {
>         r := self.radius;
>         BoundingBox {
>             x: self.center.x - r,
>             y: self.center.y - r,
>             width: r * 2.0,
>             height: r * 2.0,
>         }
>     }
> }
> ```

## Coherence [↵](#trait-impl-)

An implementation of a trait needs to be coherent, this prevents unexpected and duplicate implementations of a trait on a type.

A trait implementation is considered incoherent if any of the below rules is not followed, or overlapping implementations is encountered.

Two trait implementaion overlap when there are 2 possible implementations which can be instantiated for the same type, where neither is more specific.
For more information about this, see [trait specialization].

The coherency rules requires that a trait implementaion is only allowed if either the trait or at least one of the types within the implemented type is defined within the current library.
This prevents identical implementation from existing across different libraries.

If these types of implementations were allowed, i.e. a foreign trait being implemented for a foreign type, two overlapping trait implementation may be implemented in incompattible ways.
This could also allow a situation to occur, where adding or removing a library that is unrelated to either from breaking the compilation of the current library.
These rules also ensure that any library author can add new implementations to their traits without breaking any downstream code.

These rules are, given `impl[P1..=Pn] T0 as Trait(T1..=Tn)`, an `impl` is only valid if:
- the trait is local to the current library, or
- at least one of the types `T0..=Tn` must contain a local type
- at least one of the types in `P1..=Pn` must be bound with a local trait

To support implementations of foreign trait on foreign types, the impl must be located within an [extension].

> _Example_
> 
> library A:
> ```
> trait Foo {
>     fn(&self) do_foo();
> }
> 
> impl[T] T as Foo {
>     fn(&self) do_foo() {
>         println("Doing foo");
>     }
> }
> ```
> 
> library B
> ```
> use A:.Foo;
> use minoa:std.Heap;
> 
> trait Local {}
> struct Bar {}
> 
> // Works fine, `Bar` is local
> impl Bar as Foo {
>     fn(&self) do_foo() {
>         println("Bar does foo");
>     }
> }
> 
> // error: neither `i32` or `Foo` is local
> // impl i32 as Foo {
> //     fn(&self) do_foo() {
> //         println("i32 does foo");
> //     }
> // }
> 
> // Works fine, `Heap` is not local, but `Bar` is
> impl Heap[Bar] as Foo {
>     fn(&self) do_foo() {
>         println("Heap(Bar) does foo");
>     }
> }
> 
> // Works fine, `T` is specialized on `Local`
> impl[T is Local] T as Bar {
>     fn(&self) do_foo() {
>         println("`T is Local` does foo");
>     }
> }
> ```

## Inline implementations [↵](#implementations)
```
<inline-impl> := { <attribute> }* [ 'unsafe' ] 'impl' 'as' <path> [ <where-clause> ] '{' { <assoc-item> }* '}'
```

An inline implementation is a variant of a trait implementation, allows a trait to be implemented directly within the type definition.

> _Example_
> ```
> struct Circle {
>     radius: f64,
>     center: Point,
> 
>     impl as Copy {}
>     impl as Clone {
>         fn(&self) clone() -> Self { *self }
>     }
> 
>     // Allow `draw` and `bounding_box` to be directly called on `Circle`, e.g. `c.draw()`
>     impl as Shape {
>         fn(&self) draw(s: Surface) { draw_cirlce(s, *self); }
>         fn(&self) bounding_box() -> BoundingBox {
>             r := self.radius;
>             BoundingBox {
>                 x: self.center.x - r,
>                 y: self.center.y - r,
>                 width: r * 2.0,
>                 height: r * 2.0,
>             }
>         }
>     }
> }
> ```

## Generic implementations  [↵](#implementations)

When [generic parameters] are provided, all parameters must be used either within:
- the type being implemented
- the trait (if present)
- an associated type within the bounds

Thse are called _constained parameters_, if a parameter appears in the generica parameter list, but is not used (i.e. unconstrained), it will result in an error.

> _Example_ Valid generic implementations
> ```
> // `T` is constained by being an argument to `Struct`
> impl[T] Struct(T) {}
> 
> // `T` is constained by being an argument to Trait
> impl[T] Struct as Trait[T] {}
> 
> // `T` is constrained by being an argument to Struct, and `U` is also constrained by being part of the bound's associated type `Ty`
> impl[T, U] Struct[T] T is HasAssocType[.Ty = U] {}
> ```

> _Example_: Invalid generic implementations
> ```
> // `T` is not constrained, as it does not appear in the type
> impl[T] Struct {}
> 
> // `T` is not constrained, as it is only present within the body
> impl[T] Struct {
>     fn use_t(val: T) {}
> }
> 
> // `U` is constrained by the bound's associated type, but `T` is not constrained
> impl[T, U] Struct where T is HasAssocType[.Ty = U] {}
> 
> // `T` is used within the bound, but not with an associated bound
> impl[T, U] Struct(T) where T is Trait[T] {}
> ```


Additionally, the whitespace directly after the keyword determines whether it will be parsed as generic parameters, or the start of an array type.
This is one of the rare cases where whitespace determines how code is parsed.

> _Example_
> ```
> // Implements on the array type `[N]T`
> impl [N]T {}
> // Implementes on type `T` with the generic type `T`
> impl[T] T {}
> // Implmenets on the array type `[N]T` with the generic type `T`
> impl[T] [N]T {}
> ``` 


## Associated items [↵](#implementations)
```
<assoc-item> := <fn-item> 
              | <method>
              | <struct-item>
              | <union-item>
              | <enum-item>
              | <bitfield-item>
              | <const-item>
              | <static-item>
              | <property>
              | <inline-impl>
              | <init-item>
              | <assoc-invar-contract>
```

Associated items are items which will have path relative to the type they are part of.
The following items are allowed:
- [functions]
- [methods]
- [type aliases]
- [constants]
- [statics]
- [properties]

These item's relative path depends on the implementation used, and will be one of the following:
- when implemented on a type which can be referred to by a path: `Type.Item`
- when implemented on a type which can not be referred to by a path: `<Type>.Item`
- when implemented via a non-`extend` trait implementation: `Type.(Trait.Item)` or `<Type>.(Trait.Item)`


[extension]:            ./extensions.md
[functions]:            ./functions.md
[methods]:              ./functions.md#methods-
[type aliases]:         ./type-aliases.md
[statics]:              ./statics.md
[constants]:            ./consts.md
[properties]:           ./properties.md
[trait specialization]: ../generics.md#trait-specialization- "Todo: Section does not exists yet"
[generic parameters]:   ../generics.md#generic-parameters-
[visibility]:           ../visibility.md