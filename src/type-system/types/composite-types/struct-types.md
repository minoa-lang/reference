# Struct types
```
<struct-type>   := [ 'mut' ] 'struct' [ <generic-params> ] '{' [ <struct-members> ] '}'
<struct-members>:= [ <struct-fields> ] { <assoc-item> }*
```

A struct type or structure represents a composite type made up of named fields and associated items.
The layout of a structure is undefined by default, allowing the compiler to do optimization like field reordering.
The layout can be specified using the [`repr` attribute].

The struct needs to define all fields before any item in the struct, if any field occurs after an item will result in an error.

Support for generics can only be added to a struct type via a [struct item].

## Anonymous structs [↵](#struct-types)

An anonymous struct is what is produced when an explicit struct type is used.
These structs cannot be manually referenced, and therefore have a limited use.

Some usecases of an anonymous struct:
- to be assigned to type alias
- to be returned from a [meta function] that returns a type
- as a field of another type, allowing struct-like field access, but not have the type field explicitly usable
- as a nominal type of a local variable

> _Implementation note_: For better error reporting, the compiler will be default generate a name which includes relavent information to track it down.
>                        This behavior can be controlled explicitly in cases where this information should be restricted to only development.
> 
> The rules to decide the name are the following:
> 1. Fist a name is chosen based on the location of the struct:
>    - if used as the type of a variable, the name will be based on the name of the variable
>    - if used as a return type, the name will be based on the function it is returned by
>    - if produced by a meta function, the name will be based on the meta function
>    - if used inside of another composite type, the name will be based on the name of the composite type
>    - Otherwise, the name will be a generic name: `__anon_struct`
> 2. After this information about its span will be appended, which at minimum will contain the line and column of where the struct is defined.
>    In case this is defined within a meta function, the line and column will be taken from the location it is invoked
> 3. A pseudo-random value will be appened to the end to avoid any other name conflicts (this is not guaranteed to be stable across compilations).
>
> A compiler can enable truly random names for a struct, which will result in the struct getting a name of `__as`, followed by a psuedo-random number.

## Fields [↵](#struct-types)
```
<struct-fields> := <struct-field> { ',' <struct-field> }* [ ',' ]
<struct-field>  := { <attribute> }* [ <vis> ] [ 'mut' ] <field-names> ':' <type> [ '=' <field-defs> ] [ <field-tag> ]
<field-namesx>  := <ext-name> { ',' <ext-name> }[N]
<field-defs>    := <expr> { ',' <expr> }[N]
```

Fields make up the data which is stored within a structure type.

Each fields at minimum defines a name (or multiple), and the type of the field.
Each field can also define its visibility, which controls in which locations the field may be accessed.
In addition, a field may be made mutable, allow it to be modified after the structure is initially created, as by default each field may only be assigned when defining it in a [struct expression], and may not be modified later on.

### Field mutability [↵](#fields-) 

Each field in a structure is by default immutable, meaning it can only be set when the structure is initialized.
A field may be explicitly declared as mutable, allowing it value to be changed in other locations.

A struct type may also be defined as `mut`, this indicates that all fields within the struct will be mutable by default.
This has no effect on associated items.

> _Example_:
> ```
> mut struct {
>     a: i32,
>     b: u32,
> 
>     fn foo() {}
> }
> ```
> is equivalent to
> ```
> struct {
>     mut a: i32,
>     mut b: u32,
> 
>     fn foo() {}
> }
> ```

### Field visibility [↵](#fields-)

Each field may individually define its visibility.

This visibility defines the fields visibility relative to the location of the structure.

> _Note_: An anonymous struct do not support visibility on any field

### Multiple named fields [↵](#fields-)

A field's definition may contain multiple names, indicatin that this contains multiple fields with the same type.
Any specifier applied to this combined field will be propagated to each individual field generated from this..

> _Example_
> ```
> struct {
>     mut a, b: i32
> }
> ```
> is equivalent to
> ```
> struct {
>     mut a: i32,
>     mut b: i32,
> }
> ```

### Placeholder fields [↵](#fields-)

A placeholder field is a field inside of a struct whose primary purpose is to pad a structure.
This can be done by naming a field `_`.

This can be useful for:
- [`packed`] structs
- placeholder for future changes, while keeping the size of the structure consistent
- additional space used to avoid false sharing
- fields for FFI types which are expected to be initialized to a constant value.

If no explicit default value is provided for a placeholder field, all of its data will be filled with `0`s.

### Final slice fields [↵](#fields-)

The last field of a structure is allowed to contain a 'naked' slice.
This indicates that the type has an unbounded size, making the type `?Sized`.

It allows for a type to contain inline data within the type, e.g. like a structure stored within a file.
It is however expected to be a sequence of a known type, as this will act as if it is any other kind of [slice].

Since this field has an unknown size, any access to it is `unsafe`.

### Default struct fields [↵](#fields-)

Fields can be provided with a default value, this works for any number of names defined within a field, but requires the number of default values to match the number of names.

Providing a default value allows a field to be left out when using a [struct expression], and will instead have this default value assigned to it.
One major caveat with default values is that they have to be known at compile-time.

This means that if a field should have a default value that is calculated at compile-time, this must either be explicitly provided in the [struct expression], or be assigned within an [initializer].

> _Note_: Default values for fields should not be confused with the value of field within a `Default` implementation.
>         Unlike default field values , `Default` may provide runtime calculated values and is meant to default the entirety of the structure, not just certain fields.

> _Example_: The below structue has a compile-time known default value for `z`
> ```
> struct Foo {
>     x: i32,
>     y: i32,
>     z: i32 = 0,
> }
> ```
> this means that it can be left out during initialization:
> ```
> f := Foo {
>     x: 1,
>     y: runtime_calc(),
>     // z: 0 <- implied by the default value of `z`
> };
> ```

> _Warning_: When a certain relation between fields is expected, and at least one of them has a default value, this might introduce an error when one is assigned explicitly and the other is not.
>            Programmers should be aware of this possiblity and ensure such an error will never happen, or to give a warning, by for example having a contract check a relation between these values.

### Use fields [↵](#fields-)
```
<struct-use-field> := { <attribute> }* [ <vis> ] [ 'mut' ] [ 'extend' ] 'use' [ <name> ':' ] <path>
```

Since Minoa does not support inheritence, it can be useful to still copy fields, and optional functionality, from a struct that is used in the composition of it, in this case a `use` field can be used.

A `use` field in its simplest form will simply insert all fields of the provided struct into the body of the struct.

> _Example_: The `Bar` `use`-field will import its fields into Foo
> ```
> struct Bar {
>     x: i32,
>     y: i32
> }
> 
> struct Foo {
>     use Bar,
>     z: i32,
> }
> ```
> and will result in the following struct
> ```
> struct Foo {
>     x: i32,
>     y: i32,
>     z: i32,
> }
> ```

If after importing all `use` fields, there would be a conflicting name, an error will be generated.

#### Named use fields [↵](#use-fields-)

A `use` field may additionally be provided with a name, while this still acts the similarly to an unnamed `use` field, it allows for some additional functionality.

It allows the entirety of the `use` field to be accessed as if it were also a field by itself, which just happens to overlap all its fields.

This also includes assigning all fields associated with the `use` field to be directly passed as the named field.
```
struct Bar {
    x: i32,
    y: i32
}

struct Foo {
    use Bar,
    z: i32,
}

let bar := Bar { x: 0, y: 1 }
let foo := Foo {
    bar,
    z: 1
}
```

In addition, if any of the fields provided by the `use` field is explicitly listed in the [struct expression], the field provided to the named field will be overwritten.
```
let bar := Bar { x: 0, y: 1 }
let foo := Foo {
    bar,
    y: 3, // overwrites the `y` provided by bar.
    z: 2,
}
```

Since it is possible to access a field both directly, or via the named use field, both accesses will result in the same value
```
struct Bar {
    x: i32,
    y: i32
}

struct Foo {
    use bar: Bar,
    z: i32,
}

foo := Foo {
    x: 0,
    y: 1,
    z: 2,
}

assert(foo.z == foo.bar.z);
assert(#addr_of(foo.z) == #addr_of(foo.bar.z));
```

> _Note_: Named `use` fields add some restriction to the resulting layout of the struct as defined [here](../../type-layout.md "Todo: Fix link to correct section")

#### Extend use fields [↵](#use-fields-)

`use` field may also include any associated items defined on the imported struct, as long as they are visible from the struct importing it.

If after importing all associated items, there would be a conflicting item, an error will be generated.

> _Example_: Non-conflicting items with same name
> ```
> struct Bar {
>     x: i32,
> 
>     fn do_something(y: i32) {}
> }
> 
> struct Baz {
>     y: i32,
> 
>     fn do_something(z: i32) {}
> }
> 
> struct Foo {
>     extend use Bar,
>     extend use Baz,
> 
>     fn do_something() {}
> }
> ```
> This will not cause any conflicts, as both associated functions have different signatures.

> _Example_: Conflicting fields
> ```
> struct Bar {
>     x: i32,
> 
>     fn do_something() {}
> }
> 
> struct Baz {
>     y: i32,
> 
>     fn do_something() {}
> }
> 
> struct Foo {
>     extend use Bar,
>     extend use Baz,
> 
>     fn do_something() {}
> }
> ```
> In this case, there are 3 conflicting functions

#### Restrictions [↵](#use-fields-)

For a `use` field to be valid, it must adhere to the following restrictions:
- all fields must be visible to the struct that is importing the `use` field
- only associated items visible to the importing struct will be introduced from an `extend use` field.

Any field will have the visibility that is the [common denominator] of the visibility in the imported struct, and the visibility of the `use` field.
This also is applied to all associated items intoduced by an `extend use` field.

> _Example_
> ```
> struct Bar {
>     x: i32,
>     pub y: i32,
> }
> 
> struct Baz {
>     pub z: i32
> }
> 
> struct Foo {
>     pub use Bar,
>     pub(super) use Baz,
> }
> ```
> this will result in
> ```
> struct Foo {
>     x: i32,
>     pub y: i32,
>     pub(super) z: i32
> }
> ```

When a `use` field is marked as `mut`, it will only make imported field `mut`, it they are also declared as such in the imported struct.

> _Example_
> ```
> struct Bar {
>     x: i32,
>     mut y: i32,
> }
> 
> struct Baz {
>     mut z: i32
> }
> 
> struct Foo {
>     mut use Bar,
>     use Baz,
> }
> ```
> will result in
> ```
> struct Foo {
>     x: i32,
>     mut y: i32,
>     z: i32,
> }
> ```

#### Fields tags [↵](#fields-)
```
<struct-field-tag> := <string-literal> | <raw-string-literal>
```

Struct field tags allow arbitrary meta-information to be added to individual fields, which can be accessed using reflection.
This tag consists out of a set of key-value pairs, which in the reflection meta-data can be accessed a hashtable-like store.

A key is an indentifier that identifies the value uniquely, where as a value may be any literal.

If no key is provided, the tag's value will be stored in an array of plain values.

> _Example_:
> ```
> struct Foo {
>     a: i32 `fmt:"03"`, // Defines how this value should be formatted via the tag
> }
> ```

##### Code within field tags [↵](#fields-tags-)

It is also possible to add code with a field tags.
This is done using string interpolations, where the code will interpreted as an expression which can be executed from the reflection data.

For this reason, some limitiations on the code that can be included exists:
- the code may not use any external values, expect those coming from:
  - fields within the struct which are treated as shared references to these fields, meaning modification is not allowed
  - implicit context
- the code must return a value that can be used, there is no real limitiation on this

> _Example_:
> ```
> struct Foo {
>     is_big: bool,
>     // The format is now based on the value of `is_big`
>     value: i32 "fmt:\{ if is_big { "06" } else { "03" } }",
> }
> ```

## Annonymous struct fields [↵](#struct-types)

Anonymous struct fields allow a struct to be directly defined within a different composite type, and allows its fields to be directly accessed from that type.

> _Example_: In a union, this can ensure that a set of data does not overlap each other, and allows multiple fields to be located sequentially.
> ```
> safe union Foo {
>     value: i64le
>     struct {
>         low:  u32le
>         high: i32le
>     }
> }
> 
> f := Foo { value: 0x0123456789ABCDEF };
> 
> // We can now directly access `Foo's` `low` and `high` fields
> low := unsafe { f.low };
> ```

When a [`repr` attribute] is applied to the type containing an anonymous struct field, this attribute will also be applied to the struct field.

## Associated items [↵](#struct-types)

A struct may also directly contain associated items.
These must appear after all fields have been declared, not doing so will result in an error.

## Record structs [↵](#struct-types)
```
<record-struct-type>   := 'record' [ 'struct' ] '{' <record-struct-fields> { <assoc-item> }* '}'
<record-struct-fields> := <record-struct-field> { ',' <record-struct-field> } [ ',' ]
<record-struct-field>  := <field-names> ':' <type> [ <struct-field-tag> ]
```
A record structure, also known as a POD (Plain Old Data) struct, is a variant of a struct which is _structural_ instead of _nominal_.
These structs follow the rules defined [here](../nominal-vs-structural-types.md).

For a record struct, it is allowed to leave out the `struct` keyword.

Some of the most notable ones for structs are:
- all fields are public
- all fields are mutable

## Unit structs [↵](#struct-types)

Unit struct is a special struct that has no fields and may be constructed by just the struct's name.
The cannot be defined as a type on their own and can only be defined as a [unit struct item].

A unit struct may be seen as a distinct version of a unit type, but with the ergonomics afforded to any struct type.




[union]:              ./union-types.md
[slice]:              ../sequence-types/slice-types.md
[`packed`]:           ../../type-layout/layout-representation.md#packed
[`repr` attribute]:   ../../../attributes.md#repr-
[struct expression]:  ../../../expressions/constructing-expressions/struct-expressions.md
[initializer]:        ../../../items/initializers.md
[struct item]:        ../../../items/structs.md
[unit struct item]:   ../../../items/structs.md#unit-structs-
[meta function]:      ../../../metaprogramming.md
[common denominator]: ../../../visibility.md#common-denominator-