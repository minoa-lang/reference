# Union types
```
<union-type>    := [ 'mut' ] <union> '{' <union-elements> '}'
<union-element> := <union-field> { ',' <union-field> }* [ ',' ] { <assoc-item> }*
<union-field>   := { <attribute> }* [ <vis> ] [ 'mut' ] <name> ':' 
```

A union is a struct-like type, but instead of all fiels being sequential, a union's main characteristic is that all fields share a comon storage.
As a result, a write to any field can overwrite all other fields.
Union must always contain at least 1 field to be valid.

> _Note_: union fields may not have an anonymous generic struct type.

Each field may be made mutable, allow it to be modified after the structure is initially created, as by default each field may only be assigned when defining it in a [struct expression](../../expressions/constructing-expressions.md#struct-expressions-), and may not be modified later on.
In addition, the entire union type may also be declared as mutable, this will propagate the `mut` property to all fields, as follows:
```
mut union {
    pub a: i32,
    b: f32,
}
// Will be converted to
union {
    pub mut a: i32,
    mut b: f32,
}
```

Union field are restricted to the following sub-set of types:
- `Copy` types (including records)
- Referecnes (`&T` and `&mut T` for an arbitrary type `T`)
- Manually droppable values
- Tuples and array containing values that adhere to these restrictions

> _Todo_: Specify util for 'Manually droppable values'

These restriction ensure that no value in a field needs to be dropped.
It is also possible to manually implement `Drop` on union, as by default, union fields will never be dropped.

#### Union field access [↵](#union-types)

Because of the common storage of unions, when initializing a union, only 1 field may be initialized.
The field that is initialized is said to be the `active` field of the union.

> _Note_: Unions do not have an actual notion of an `active` field, i.e. no metadata is carried to specify the active field, so there is no special meaning.
>         The term is meant to specify to a programmer what union field is currently assigned and has a valid value.

While any member can be accessed at any time, by reading a value from the underlying memory, it is recommended only to ever access the 'active' value.
This is because any field with an incompatible layout with the active field **may** contain invalid data.

Because of this, reading a union field is an `unsafe` operation.
Writing to a union field is always safe on the other hand, as it overwrites arbitrary data.

Reading a field that is not the active field in the union is illegal behavior.

Safety checks will always be added when the compiler is able to figure out whether a field is accessed incorrectly from the surrounding context.
While it is not possible in compiled code, when the code is run through an interpreter, it is allowed to store additional metadata separatly from the data, and add safety check at an access site.

Unless a union has a `C` representation, it is also illegal behavior to transute the value to any type, as there is not guarantee that all types are stored at a zero-offset.
With a `C` representation, reading from the union is analogous to transmuting its data to one of the fields types.

#### Pattern matching on unions [↵](#union-types)

Unions can also be accesed inside when pattern matching, with the restiction that exactly one field is allowed to be matched at any time.
Since reading from a pattern field is unsafe, the corresponding match must be done in an unsafe context.

Pattern matching directly on a union is not allowed, it is only allowed when it is part of a larger structure when there is a separate disambiguation in addition to the union, like a value coming from an FFI context.

For Example
```
union U {
    i: i32,
    f: f32
}

u := U{ i: 1 };

// error: cannot match on a union
unsafe {
    match u {
        .{ i: 1 } => (),
        .{ f } => (),
    }
}

enum Tag { I, F };
struct Value {
    tag: Tag,
    u:   U,
}

val := Value { tag: Tag.I, u };
// Fine, as we aren't directly switching on a union, but a stucture containin a union
unsafe {
    match val {
        .{ tag: .I, u: .{ i: 1 } } => (),
        .{ tag: .F, u: .{ f } } => (),
        _ => ()
    }
}


// But the following will:
// error: cannot match on a union field only
unsafe {
    match val {
        .{ u: .{ i: 1 }, .. } => (),
        _ => ()
    }
}
```

#### Reference to union fields [↵](#union-types)

Since union fileds share a common storage, gaining writing access to one field of hte union can give write access to all its remaining fields.
Therefore, if any field is borrowed mutably, no ther field can be borrwoed mutably at the same time.
