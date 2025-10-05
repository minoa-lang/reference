# Union types
```
<union-type>    := <type-layout-specifiers> [ 'mut' ] [ 'safe' ] <union> '{' <union-elements> '}'
<union-element> := <union-field> { ',' <union-field> }* [ ',' ] { <assoc-item> }*
```

A union is a struct-like type, but instead of all fiels being sequential, a union's main characteristic is that all fields share a common storage.
As a result, a write to any field can overwrite all other fields.
Union must always contain at least 1 field to be valid.

_Note_: For an equivalent of a tagged union, see [enums].

## Anonymous unions [↵](#union-types)

An anonymous union is what is producces when an explicit union type is used.
These union cannot be manually referenced, and therefore have a limited use

Some usecases of an anonymous union:
- to be assigned to type alias
- to be returned from a [meta function] that returns a type
- as a nominal type of a local variable

> _Implementation note_: For better error reporting, the compiler will be default generate a name which includes relavent information to track it down.
>                        This behavior can be controlled explicitly in cases where this information should be restricted to only development.
> 
> The rules to decide the name are the following:
> 1. Fist a name is chosen based on the location of the struct:
>    - if used as the type of a variable, the name will be based on the name of the variable
>    - if produced by a meta function, the name will be based on the meta function
>    - if used inside of another composite type, the name will be based on the name of the composite type
>    - Otherwise, the name will be a generic name: `__anon_union`
> 2. After this information about its span will be appended, which at minimum will contain the line and column of where the struct is defined.
>    In case this is defined within a meta function, the line and column will be taken from the location it is invoked
> 3. A pseudo-random value will be appened to the end to avoid any other name conflicts (this is not guaranteed to be stable across compilations).
>
> A compiler can enable truly random names for a struct, which will result in the struct getting a name of `__au`, followed by a psuedo-random number.

## Fields
```
<union-field>   := { <attribute> }* [ <vis> ] [ 'mut' ] <name> ':' <type>
```

Fields are similar to those of struct, except of the above mentioned caveat where all fields have a common storage.
A union needs to have at least 1 field, otherwise it will result in an error.

Each field defines a singular name and the type of the field.
Each field can also define its visibility, which controls in which location the field may be accessed.
In addition, a field may be made mutable, allow it to be modified after the structure is initially created, as by default each field may only be assigned when defining it in a [struct expression], and may not be modified later on.

### Field mutability [↵](#fields-) 

Each field in a union is by default immutable, meaning it can only be set when the union is initialized.
A field may be explicitly declared as mutable, allowing its value to be changed in other locations.

A union type may also be defined as `mut`, this indicates that all fields within the struct will be mutable by default.
This has no effect on associated items.

> _Example_:
> ```
> mut union {
>     a: i32,
>     b: u32,
> 
>     fn foo() {}
> }
> ```
> is equivalent to
> ```
> union {
>     mut a: i32,
>     mut b: u32,
> 
>     fn foo() {}
> }
> ```

### Field visibility [↵](#fields-)

Each field may individually define its visibility.

This visibility defines the fields visibility relative to the location of the union.

> _Note_: An anonymous union do not support visibility on any field

### Union field access [↵](#fields-)

Since unions use common storage, when initializing a union, only 1 field may be initialized.
The field that is initialized is said to be the 'active' field of the union.
The active field can be 'set' by writing to the field that needs to be active.

> _Note_: Unions do not have an actual notion of an 'active' field, i.e. no metadata is carried to specify the active field.
>         The term is meant to specify to a programmer what union field is currently assigned and has a valid value.

While any member can be accessed at any time, by reading a value from the underlying memory, it is recommended only to ever access the 'active' value.
This is because any field with an incompatible layout with the active field **may** contain invalid data.

Because of this, reading a union field is an `unsafe` operation.
Writing to a union field is always safe on the other hand, as it overwrites arbitrary data.

Reading a field that is not the active field in the union is [illegal behavior].

IF the compiler is able to figure this out at compile time, it will generate an error if any path that can get to the read would have a different field 'active'.
While it is not possible in compiled code, when the code is run through an interpreter, it is allowed to store additional metadata separatly from the data, and add safety check at an access site.

> _Note_: If 2 types need to be converted by interpreting the data as a different type, a [transmute] should be used instead.

### Restrictions [↵](#fields-)

Each union field is restricted to the following subset of types:
- `Copy` types (including records)
- pointer-like types
- Manually dropped values
- Tuple and arrays containing values that adhere to these restrictions

These type restrictions ensure that no value in a field needs to be dropped.
It is also possible to manually implement `Drop` on a union, as by default, union fields are never dropped.

With the default [`minoa` type layout], there is no guarantee that fields are laid out starting at offset 0.

Types may be marked as `@no_union`, in this case, these types may not be used within a union, even when wrapped in another type.
This attribute is auto-propagating, meaning that if a `@no_union` type is used as a field in another type, it itself becomes `@no_union`.

## Anonymous union fields [↵](#union-types)

Anonymous union fields allow a structure to be directly defined within a different composite type, and allows its field to be directly accessed from the union.

> _Example_
> ```
> struct Foo {
>     is_float: bool,
>     union {
>         i: i32,
>         f: f32,
>     }
> }
> 
> f := Foo { ... };
> 
> // We can not directly access `Foo`'s `i` and `f` fields
> i := unsafe { f.i };
> ```

When a [`repr` attribute] is applied to the type containing an anonymous union field, this attribute will also be applied to the union field.

> _Note_: Since these fields are stored inside a union, accessing these particular fields need to happen in an `unsafe` context.

## Safe unions [↵](#union-types)

Unions can be declared as `safe`, this lifts certain restrictions when it comes to union field access.
A `safe` union indicates that the union is guaranteed to always have a valid representation for each field.
In addition, it also ensures that all fields in the union will have a memory offset of 0.

These 2 additional rules losen the restriction of the `active` field.
To be more precise, it gets rid of the notion of an active field, since it is guaranteed that each field will always have a valid value.

`safe` unions also allow all their field to be accessed from outside of an `unsafe` context.

If types are marked as `@no_transmute`, in this case, these types may not be used within a `safe` union.

## Pattern matching on unions [↵](#union-types)

Unions can also be accesed inside when pattern matching, with the restiction that exactly one field (or all fields in an anonymous field) is allowed to be matched at any time.

If reading from a pattern field is unsafe (i.e. not from a `safe` union), the corresponding match must be done in an unsafe context.

Pattern matching directly on a non-`safe` union is not allowed, it is only allowed when it is part of a larger structure when there is a separate disambiguation in addition to the union, like a value coming from an FFI context.

> _Example_: Trying to match on a union field only will result in an error
> ```
> union U {
>     i: i32,
>     f: f32
> }
> 
> u := U{ i: 1 };
> 
> // error: cannot match on a union
> unsafe {
>     match u {
>         .{ i: 1 } => (),
>         .{ f } => (),
>     }
> }
> ```
> But this is allowed for a safe union
> ```
> // Note that this is an example, and converting between an i32, and f32 should be done via an explicit transmute
> safe union U {
>     i: i32,
>     f: f32
> }
> 
> u := U{ i: 1 };
> 
> // this is fine, the union is expliclty defined as safe
> unsafe {
>     match u {
>         .{ i: 1 } => (),
>         .{ f } => (),
>     }
> }
> ```
> 
> This can be fixed by adding an additional field that is used to match the value
> ```
> enum Tag { I, F };
> struct Value {
>     tag: Tag,
>     u:   U,
> }
> 
> val := Value { tag: Tag.I, u };
> // Fine, as we aren't directly switching on a union, but a stucture containin a union
> unsafe {
>     match val {
>         .{ tag: .I, u: .{ i: 1 } } => (),
>         .{ tag: .F, u: .{ f } } => (),
>         _ => ()
>     }
> }
> 
> ```
> But the following (on a non-`safe` union) will cause an error
> ```
> // error: cannot match on a union field only
> unsafe {
>     match val {
>         .{ u: .{ i: 1 }, .. } => (),
>         _ => ()
>     }
> }
> ```

## Reference to union fields [↵](#union-types)

Since union fields share a common storage, gaining writing access to one field of the union can give write access to all its remaining fields.
Therefore, if any field is borrowed mutably, no ther field can be borrwoed mutably at the same time.



[manually dropped]:    #fields "Todo: Link to ManualDrop docs"
[transmute]:           #fields "Todo: Link to transmute"
[enums]:               ./enum-types.md
[`minoa` type layout]: ../../type-layout/composite-layout.md#minoa-representation-
[`repr` attribute]:    ../../../attributes.md#repr-
[struct expression]:   ../../../expressions/constructing-expressions.md#struct-expressions-