# Tuple struct types
```
<tuple-struct>           := [ 'mut' ] 'struct' '(' [ <tuple-fields> ] ')' [ <tuple-stuct-body> ] ';'
<tuple-struct-fields>    := <tuple-field> { ',' <tuple-field> }* { ',' <tuple-struct-def-field> }* [ ',' ]
                          | <tuple-struct-def-field>  { ',' <tuple-struct-def-field> }* [ ',' ]
<tuple-field>            := [ <vis> ] [ 'mut' ] [ <name> ':' ] <type>
<tuple-struct-def-field> := [ <vis> ] [ 'mut' ] [ <name> ":" ] <type> '=' <expr>

<tuple-struct-body>      := '{' { <struct-member> } '}'
```

A tuple struct type is similar to a struct type, with some differences.
Like both structs, the internal representation of a tuple struct is by default undefined, allowed for the same compiler optimizations.

Each field within the tuple struct can have its visibility and mutability defined.
Each field can be accessed using a [tuple index expression], in addition, they may also have an optional name, which may be used to access a field via a [field access expression].

Similar to a regular stuct type, the entire tuple struct type may be marked as `mut`, which will then propage the `mut` property to all fields.

Tuple struct allows associated item to be added at the definition's location, by adding a special body after the tuple declaration.

#### Default tuple struct fields [↵](#tuple-struct-types)

A field may contain a default value, if they are placed at the end of the tuple fields, and may only followed by other fields with a default value.
Meaning that when these fields are left out during initialization, they will gain this value.
The default vbalue needs to be known at compiler time, in case a field should have a default value that can only be calulated at runtime, a function can be used.

> _Note_: Default values for fields should not be confused with the value of fields if the `Default` trait is implemented.
> Field default values are used to allow them to be omitted when constructing a new struct, not to retrieve a default value for the entire struct,
> this means that `Default::default()` may return a different value that a field's individual default value, as it is allowed to decide these values at runtime.

```
// Foo has a default value for the last field, as this can be calculated at compile time
struct Foo(i32, i32, i32 = 3);

// If we now want the 2nd field to always have a default value, but is required to be calculated at runwime, we would use a function instead
impl Foo {
    pub fn new(x: i32) -> Self {
        Self (
            x,
            runtime_calculation,
            // 3 <- default value is always 3
        )
    }
}
```

#### Record tuple structs [↵](#tuple-struct-types)
```
<record-tuple-struct>           := 'record' 'struct' '(' [ <tuple-fields> ] ')' ';'
<record-tuple-struct-fields>    := <tuple-field> { ',' <record-tuple-field> }* { ',' <record-tuple-struct-def-field> }* [ ',' ]
                                 | <record-tuple-struct-def-field>  { ',' <record-tuple-struct-def-field> }* [ ',' ]
<record-tuple-field>            := [ <name> ':' ] <type>
<record-tuple-struct-def-field> := [ <name> ":" ] <type> '=' <expr>
```

A record tuple struct, also known as a POD (Plain Old Data) tuple struct, is a variant of a tuple struct which is _structureal_ instead of _nominal_.
These tuple struct follow the rules defined [here](../nominal-vs-structural-types.md).

Some of the most notable ones for tuple structs are:
- All fields are public
- All fields are mutable



[field access expression]: ../../expressions/field-access-expressions.md
[tuple index expression]:  ../../expressions/tuple-index-expressions.md