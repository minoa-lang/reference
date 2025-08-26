# Struct types
```
<struct-type>   := [ 'mut' ] 'struct' [ <generic-params> ] '{' [ <struct-members> ] '}'
<struct-members>:= [ <struct-fields> ] { <assoc-item> }*

<struct-fields> := <struct-field> { ',' <struct-field> }* [ ',' ]
<struct-field>  := { <attribute> }* [ <vis> ] [ 'mut' ] <field-names> ':' <type> [ <struct-field-tag> ]
                | { <attribute> }* [ <vis> ] [ 'mut' ] <name> ':' <type> [ '=' <expr> ] [ <struct-field-tag> ]
<field-names>   := <name> [ ',' <name> ]
```

A structure type represents a collection of named fields, and associated items.
The layout of a structure is undefined by default, allowing the compiler to do optimization like field reordering. The layout can be specified using the [`repr` attribute](../../attributes.md#repr-).

The struct needs to define all fields before any item in the struct, if any field occurs after an item will result in an error.

Each fields at minimum defines a name (or multiple), and the type of the field.
Each field can then define its visibility, which controls in which locations the field may be accessed.
In addition, a field may be made mutable, allow it to be modified after the structure is initially created, as by default each field may only be assigned when defining it in a [struct expression](../../expressions/return-expressions.md), and may not be modified later on.

> _Note_: struct fields may not have an anonymous generic struct type.

In addition, the entire struct type may also be declared as mutable, this will propagate the `mut` property to all fields, as follows:
```
mut struct {
    pub a: i32,
    b: i32,
}
// Will be converted to
struct {
    pub mut a: i32,
    mut b: i32,
}
```

Each field definition may contain multiple names, this will result in a field to be created for each, with the type that was defined.
When multiple names are used, both the visibility and mutability will also be propagated to the created fields
Below is an example of this.
```
struct {
    pub mut a, b: i32,
}
```
Will be equivalent to
```
struct {
    pub mut a: i32,
    pub mut b: i32,
}
```

A field may also be a placeholder field, by using `_` as its name, this means that the field will take up space within the structure, but will not be accessible.
This can be used for example as padding in a [`packed`](../type-layout/layout-representation.md#packed) struct, or as a reserved value for future use.

After all fields are declared, a struct may also define associated items.

> _Note_: A [struct item](../../items/structs.md) allows for an easy way of defining structures.

> _Todo_: A link to associated items

#### Default struct fields [↵](#struct-types)

When a field has only a single name defined for it, the field may be provided with a default value, this means that when a field is not explicitly initialized, i.e. when left out, the field will gain this value.
The default value needs to be known at compile time, in case a field should have a default value that can only be calculated at runtime, a function can be used.

> _Note_: Default values for fields should not be confused with the value of fields if the `Default` trait is implemented.
> Field default values are used to allow them to be omitted when constructing a new struct, not to retrieve a default value for the entire struct,
> this means that `Default::default()` may return a different value that a field's individual default value, as it is allowed to decide these values at runtime.

```
// Foo has a default value for `z`, as this one can be calculated at compile-time
struct Foo {
    x: i32,
    y: i32,
    z: i32 = 3,
}

// If now we want `y` to always have a default value, but it requires to be calculated at runtime, we would use a function instead
impl Foo {
    pub fn new(x: i32) -> Self {
        Self {
            x,
            y: runtime_calculation(),
            // z: 3 <- default value is always 3
        }
    }
}
```

#### Use fields [↵](#struct-types)
```
<struct-use-field> := { <attribute> }* [ <vis> ] [ 'mut' ] [ 'extend' ] 'use' [ <name> ':' ] <path>
```

While a structure cannot derive from another structure, it is able to include the content of another struct in its body and even take over functionality.
This can be done using a `use` field, in a structure, `use` does not import symbols, but creates a use field.

A use field can be named, in addition to be able to access the include struct's method directly, the contents of the struct has to be access via its name, as if it were a member.
Either will still refer to the same data, meaning:
```
struct Bar {
    a: i32
}

struct Foo {
    x: f32,
    // Named use field
    use bar: Bar,
}

foo := Foo { x: 2.0, a: 3 };
assert(addr_of(foo.x) == addr_of(foo.bar.x));
```
> _Todo_: Fix `assert` and `addr_of` syntax

If a use field contains a variable with the same name of another variable, or another variable in another use field, an error will be emitted.
This can be resolved by having a named use field, but a warning will still be generated.
```
struct Foo {
    a: i32,
    b: i32
}

struct Bar {
    b: i32
}

struct A {
    a: i32,
    use Foo, // error: field `Foo.a` overlaps field `a`
}

struct B {
    use Foo,
    use Bar, // error: field `Bar.b` overlaps field `Foo.a`
}

// This also happens for named use fields
struct A {
    a: i32,
    use foo: Foo, // warning: field `foo.a` overlaps field `a`, and can therefore only by accesed via `A.foo.a`
}
```

The above struct can  also be initialized via the include struct's name, as shown below:
```
bar := Bar { a: 4 };
foo := Foo { x: 1.0, bar };
```

The following restrictions are applied to the `use` field's included struct:
- All fields need to be visible to the structure with the field
- Only items visible to the structure with the field will be implemented.

The visibility and mutablity of the fields depends on the visibility and mutability provided to the included struct.
These are applied to all fields within the included struct, and to the struct name (if provided).

Visibility and mutability will be applied in the following way:
- A field's visibility will be the [common denominator](#162-common-denominator-) of the field visibility and the provided visibility
- A field's mutability will be a combination of the provided mutability and the field's visibility within the included struct, meaning both must be `mut` for the included field to be `mut`

In addition, the functionality of the included struct can be automatically implemented on the new struct, by using `extend`.
The visibility of these items will be applied in the same way as that of field, using the [common denominator](#162-common-denominator-) of the visibilities.

Only items that would not override an already existing item will be implemented.
If multiple `extend` use fields exists, and an item would conflict, an error will be produced.
```
struct Foo {
    x: i32

    impl {
        fn foo() {}
    }
}

struct Bar {
    y: i32

    impl {
        fn foo() {}
        fn bar() 
    }
}

struct A {
    extend use Foo,
    extend use Bar, // error: Conflicing item `fn foo()`
}

struct B {
    use Foo,
    extend use Bar, // While `Bar` contains `foo`, it's explicitly overwritten by `B`, so `Bar.foo` will be ignored

    impl {
        fn foo() {}
    }
}
```

#### Fields tags [↵](#struct-types)
```
<struct-field-tag> := <string-literal> | <raw-string-literal>
```

Struct field tags allow arbitrary meta-information to be added to individual fields, which can be accessed using reflection.

#### Record structs [↵](#struct-types)
```
<record-struct-type>   := 'record' 'struct' '{' <record-struct-fields> { <assoc-item> }* '}'
<record-struct-fields> := <record-struct-field> { ',' <record-struct-field> } [ ',' ]
<record-struct-field>  := <field-names> ':' <type> [ <struct-field-tag> ]
```
A record structure, also known as a POD (Plain Old Data) struct, is a variant of a struct which is _structural_ instead of _nominal_.
These structs follow the rules defined [here](../nominal-vs-structural-types.md).

Some of the most notable ones for structs are:
- all fields are public
- all fields are mutable

#### Unit structs [↵](#struct-types)
```
<unit-struct-type> := 'struct' '{' [ <struct-element> ] '}'
```

A unit struct is a variant of a struct containing no fields, and which can be initialized using just their name.
They can be seen as distinct type aliases to a unit type, but with the ergonomics afforded to any structure type.

A unit struct cannot be anonymous and must be associated with a type alias.

#### Generics in struct types [↵](#struct-types)

Struct types may also contain generic arguments, how this is handled depends on whether it is assigned to a type alias.
If it is assigned to a type alias, the arguments can be directly provided at the usage location of the struct.
When it is directly used as a type of a variable, it must be inferred from the values passed to the value on initialization.
