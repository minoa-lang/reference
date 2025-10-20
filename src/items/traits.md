# Traits
```
<trait-item> := { <attribute> }* [ <vis> ] [ 'unsafe' ] [ 'sealed' ] 'trait' <name> [ <generic-prams> ] [ ':' <trait-bounds> ] [ <where-clause> ] '{' { <trait-item> }* '}'
```

A trait defines an abstract interface which can be implemented by types.
Each trait may also include an additional set of [trait items].

Traits are implemented using either an [inline] or [external trait `impl`].

In addition, each trait has access to an implicit `Self` type within all of its items.
This represents the type which implements the trait, and not the trait itself.

Traits may also contain additional generic parameters, which may include `Self`, and may be constrained by additional requirements.




## Sealed traits [↵](#trait)

A sealed trait is a trait which limits any implementations of it to the current library.
This can be used to mark a specific set of types, without allowing this to be defined externally.

> _Example_
> Lib a:
> ```
> trait Foo {}
> 
> sealed trait Bar {}
> 
> // Both can be implemented from the current library
> impl i32 as Foo {}
> impl i32 as Bar {}
> ```
> Lib b:
> ```
> struct Struct {};
> 
> // Fine, `Foo` is just a regular trait
> impl Struct as Foo {}
> 
> // error: `Bar` is a sealed trait
> impl Struct as Bar {}
> ```

## Unsafe traits [↵](#trait)

Undafe trait have additional requirements on top of the standard trait requirements.
Specifically, it provides a given set of guarantees which cannot be determined by the compiler.
It is up to the implementator to ensure that these guarantees are followed.

Implementing an unsafe trait must be done via an `unsafe impl`.

## Dyn compatibility [↵](#trait)

`dyn` compatibility specifies a set or requirements that must be adhered to be able to use a trait in places where a trait object is allowed.
These rules are the following:
- all supertraits must be object safe
- the trait cannot be sized, i.e. it may not have a `Self is Sized` bound
- the trait must not have associated constants
- the trait must not have any associated types using generics
- all associated functions and methods must either be:
  - dispatchable, requiring:
    - must be a method
    - not have any type parameters or deduced parameters
    - not use `Self` outside of the receiver
    - have a receiver which allows for dynamic dispatching, i.e. `&self`, `&mut self`, or any types implementing `DispatchFomDyn`
    - not have any inferred parameter or return types, i.e. `impl` types
    - not have an explicit or implied `where Self is Sized` bound
  - explicitly non-dispatchable, requiring:
    - have a `where Self is Sized` bound

> _Note_: The async function traits are not dyn compatible

> _Example_: Dyn compatible trait
> ```
> trait Foo {
>     fn(&self)                 by_ref();
>     fn(&mut self)             by_mut();
>     fn(self: DynDispatchable) by_dispatchable();
> }
> ```

> _Example_ Nondispatchable items
> ```
> trait NonDispatchable {
>     // Non-method
>     fn foo() where Self is Sized {}
>     // Method with Self bound
>     fn(&self) method() where Self is Sized {}
>     // returning Self
>     fn(&self) returns() -> Self where Self is Sized;
>     // having Self as a parameter
>     fn(&self) param(arg: Self) where Self is Sized {}
>     // having constant parameters
>     fn(&self) const_params(ty: type) where Self is Sized {}
>     // having deduced parameters
>     fn(&self) deduced[const T: type](val: T) Where Self is Sized {}
> }
> 
> struct S;
> impl S as NonDispatchable {
>     fn(&self) returns() -> Self wher Self is Sized {
>         Self
>     }
> }
> 
> s := DynWrapper[dyn S]();
> s.foo();           // error: `foo` is a non-dispatchable function with Sized bound
> s.method();        // error: `method` is a non-dispatchable method with Sized bound
> s.returns();       // error: `returns` is a non-dispatchable method returning a `Self` type
> s.param(S);        // error: `param` is a non-dispatchable method taking in a `Self` parameter
> s.const_params(s); // error: `const_params` is a non-dispatchable method taking in a `const` parameter
> s.deduced(3i32);   // error: `deduced` is a non-dispatchable method with deduces parameters
> ```

> _Example_: Incompatible trait items
> 
> Error shows show error when trying to use type as an associated item.
> ```
> trait Incompatible {
>     // error: dyn compatible type cannot have associated constant
>     const CONST: i32 = 3;
> 
>     // error: dyn compatible type cannot have function without a `Self is Sized` bound
>     fn foo() {}
> 
>     // error: dyn compatible type cannot return Self without a `Self is Sized` bound
>     fn(&self) return() -> Self;
> 
>     // error: dyn compatible type cannot have a const parameters without a `Self is Sized` bound
>     fn(&self) typed(const T: type) {}
> 
>     // error: dyn compatible type cannot have a deduced parameter without a `Self is Sized` bound
>     fn(&self) deduced[T: type](val: T) {}
> 
>     // error: dyn compatible type cannot have a receiver that is not `DispatchFomDyn(Self)` without a `Self is Sized` bound
>     fn(self: DynWrapperA<DynWrapperB<Self>>) nested() {}
> }
> 
> struct S;
> impl S as Incomaptible {
>     fn(&self) returns() -> Self { Self }
> }
> 
> s := DynWrapper[dyn S](); // error: `S` is not dyn compatible
> ```

> _Example_: `Self is Sized` trait
> ```
> trait SizedTrait where Self: Sized {}
> 
> struct S;
> impl S as SizedTrait {}
> 
> s := DynWrapper[dyn S](); // error: `S` is not dyn compatible
> ```

_Example_ 

## Supertraits [↵](#trait)

A supertrait is a requirement that an implementation must follow, and are defined as trait bound on the current trait.
In other words, they are traits that already need to have been implemented on a type, to allows the implementation of the current trait.

The trait relying on the supertrait is also called a subtrait.
Trait cannot both be supertraits and subtraits of the same trait.

Trait may also not have themselves as subtraits, i.e. supertraits may **not** form a circular dependency.

Any supertraits are additionally guaranteed to be implemented on `Self`.
And adding a trait bound to `Self` will automatically define the traits in the bound as a supertrait of the current trait.

> _Example_
> ```
> trait Shape { fn(&self) area() -> f64; }
> trait Circle: Shape { fn(&self) radius() -> f64; }
> ```

> _Example_: Supertrait via bound on self
> ```
> trait Shape { fn(&self) area() -> f64; }
> trait Circle where Self is Shape { fn(&self) radius() -> f64; }
> ```

> _Example_: Using supertrait items within a trait
> ```
> trait Shape { fn(&self) area() -> f64; }
> trait Circle: Shape {
>     fn(&self) radius() -> f64 {
>         // From the area of a circle:
>         // area = r^2 * PI
>         // We can drive the following equation:
>         // Note that we are using `Shape.area` here
>         (self.area() / PI).sqrt()
>     }
> }
> ```

## Visibility [↵](#trait)

Any item within the trait may not define its own visibility, instead it will take on the visibility of the interface.
If different sets of items require a different visibility, this should be done via multiple implementations.

## Trait items [↵](#trait)
```
<trait-item> := <trait-fn>
              | <trait-method>
              | <trait-type-alias>
              | <trait-const>
              | <trait-property>
```

Trait items are special variants of a subset of items, which are the following
- [functions & methods]
- [type aliases]
- [constants]
- [properties]

These items defined which items a type must implement to be able to be compliant with a given trait.
Each of these may additionally provide a default implementation.


[trait items]:           #trait-items-
[functions & methods]:   ./functions.md#trait-functions--methods-
[type aliases]:          ./type-aliases.md#trait-type-aliases-
[constants]:             ./consts.md#trait-constant-
[properties]:            ./properties.md#trait-properties-
[inline]:                ./implementations.md#inline-trait-impls- "Todo: Section does not exist yet"
[external trait `impl`]: ./implementations.md#external-trait-impls-