# Field access expressions
```
<field-access-expr> := <expr> [ '?' ] '.' ( <ext-name> | <path-disambig> )
```

A field expression can refer to one of the following:
- a [place expression] that evaluates to the location of a field of a type
- a value defined by a [property]

When the oprand is mutable, the return field is also generally mutable, although this can depend on the provided [accessors] when the field refers to a property.

> _Note_: If a field access is followed by parentheses, it will be interpreted as a 

> _Example_
> ```
> struct Foo {
>     bar: i32,
> }
> 
> foo := Foo { bar: 42 };
> 
> assert(foo.bar == 42);
> ```

## Optional chaining [↵](#field-access-expressions)

Field accesses support a feature known as optional chaining, or null-propagation.
This allows for a field's value to only be retrieved when the operand it is called on has a valid value, otherwise it will not execute the access and just return the current value.
This would also end the chain of expression directly applied to the result of this expression.

This operation is handled by the `OptAccess` trait and works on all type implementing it.

> _Example_
> ```
> struct Foo {
>     bar: i32,
> }
> 
> a := Foo{ bar: 42 };
> b: ?Foo = null;
> 
> assert(a.bar == 42);
> 
> // the `null` value is propagated and the field access is skipped
> assert(a?.bar == null);
> ```

## Automatic dereferencing [↵](#field-access-expressions)

If the type of the left-hand operand implements `Deref`, or `DerefMut` when the operand is mutable, it is automatically dereferences as many times as are needed to make the field access possible.
This process is known as _auto-deref_ for short.

> _Example_
> ```
> struct A {
>     a: i32,
>     b: f32
> }
> 
> struct B {
>     val: A,
>     c: i32
> 
>     impl as Deref {
>         // ...
>     }
> }
> 
> val := B { val: A { a: 1, b: 2.0 }, c: 3 };
> 
> // directly accessing a value in `B`
> assert(val.c == 3);
> 
> // will deref the value before accessing the fields of `A`
> assert(val.a == 1);
> assert(val.b == 2.0);
> ```

## Borrowing [↵](#field-access-expressions)

How a field access will borow a value depends on what the field points to:
- if it points to an actual field, the borrow will be of that field alone
- if it points to a property, it will depend on which [accessors] are available
  - when the directly corresponding accessor (`ref get` for `&`, and `mut get` for `&mut`) is available, it will borrow the specific value
  - when only a `get` accessor is available, only an immutable reference (`&`) may be taken, unless it is allowed on the value returned.
    This is done to prevent any confusion which may be caused when `&mut val.field` would create a mutable reference to a temporary

Every borrow of a field or propery is treated as a separate entity when borrowing.
In case of a property, which will borrow all fields used in the returned value of that property.

If a type does not implement `Drop` and is stored as a local variable, this also applies to moving out of each field.
However, this has no impact on properties, as a non-borrowing property does not move out, but only create a new value.
This also does not appy if automatic dereferencing is done through user defined type that don't support `DerefMove`

> _Example_
> ```
> struct Foo {
>     s0: String,
>     s1: String,
>     s2: String,
>     s3: String,
>     s4: String,
> 
>     prop p0: String {
>         get { self.s3.clone() }
>         ref get { &self.s3 }
>         mut get { &mut self.s3 }
>     }
> 
>     prop p1: Borrowable[String] {
>         get { Borrowable(&self.s4) }
>     }
> }
> 
> mut x: A = ...;
> 
> a := &mut x.s0; // borrows `s0` mutably
> b := &x.s1; // borrows `s1`
> c := &x.s1; // also borrows `s1`
> d := x.s2; // moves out of `s2`
> 
> e := &x.p0; // borrows `s0`
> f := &mut x.p0; // borrows `s1`
> g := x.p0; // does not move out any value, as this produces a new value
> 
> h := x.p1; // borrows `s4`
> ```

[place expression]: ../expressions.md#place-expressions-
[property]:         ../items/properties.md
[accessors]:        ../items/properties.md#accessors-