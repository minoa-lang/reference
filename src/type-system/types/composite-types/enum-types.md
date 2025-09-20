# Enum types
```
<enum-type>           := [ 'mut' ] 'enum' '{' <enum-members> '}'
<enum-members>        := <enum-variant> { ',' <enum-variant> } [ ',' ] { <assoc-item> }*

<enum-variant>        := <unit-enum-variant>
                       | <tuple-enum-variant>
                       | <struct-enum-variant>

<unit-enum-variant>   := <name> [ '=' <expr> ] [ <field-tag> ]
<tuple-enum-variant>  := [ 'mut' ] <name> '(' <tuple-field> { ',' <tuple-field> }* [ ',' ] ')' [ '=' <expr> ] [ <field-tag> ]
<struct-enum-variant> := [ 'mut' ] <name> '{' <struct-field> { ',' <struct-field> } '}' [ '=' <expr> ]
```

An enum type, also known as an enumeration, is a collection of distinct values, called variants, which may have additional data attached to them, known as a variant's payload.
Since an enum can carry a payload, this is also sometimes know as an algebraic data type.

Each variant is also its own 'constructor', creating an enum value of the given variant with its associated data.
Enums are particularly useful in when used for pattern matching.

An enum is required to at minimum have a single variant.

Each variant can be on of the following:
- unit variant, also known as a fieldless variant: declared with just a name, i.e. it has no payload
- tuple variant: a name, followed by a tuple defintion
- struct varaiant: a name, followed by a struct body with only fields

Each field in a variant may be made mutable, allow it to be modified after the structure is initially created, as by default each field may only be assigned when defining it in a [struct expression], and may not be modified later on.

> _Note_: struct fields may not have an anonymous generic struct type.

Defining mutability may also happen on a per-variant basis, or on the entire enum type, this will propagate the `mut` property to all fields of the variant, or the entire enum, respectivly, as follows:
```
enum {
    mut A { b, c: i32 },
    D(i32, f32),
}
// Will be converted to
enum {
    mut A { mut b, c: i32 },
    D(i32, f32),
}

// Or
mut enum {
    A { b, c: i32 },
    D(i32, f32),
}
// Will be converted to
enum {
    mut A { mut b, c: i32 },
    D(mut i32, mut f32),
}
```


Each variant works as its own symbol, and can therefore be imported using a `use`, this allows the variant to be used without preceeding its name by either a path or a inferring `.` (dot).

Each variant may also have a field tag associated with it.

> _Todo_: Could be interesting to add a version with an external discriminant, i.e. a discriminant that's separate from the actual payloads, which isn't just a union.
>         i.e. instead of matching to `{ tag: .Val, union_val: { name: val } }`, we could match on `Val{ name: val }`, i.e. something like `data.with_discriminant(val)`

#### Discriminant [↵](#enum-types)

Each variant is represented using its discriminant, this is an integer value that logically associates an enum instance with the variant an enum holds.

By default, this value is represented as an `isize` value.
However, the compiler is allowed to substitute this with a smaller integer types that fits all determinants, including unsigned types if no distriminant needs to hold a signed value.
This is in order to reduce the amount of memory that is taken in by each enum instance.

The underlying type discriminant is represented by, can be defined explicitly using a [`repr` attribute].

> _Todo_ Add support for non-integer discriminants, e.g. char

##### Assigning discriminant values [↵](#discriminant-)

By default, the discriminant is implicitly assigned, each varaint will take on the discriminant of the previous value, plus one.
If the intial variant has no value assigned, it will get a value of 0.

Discriminants can also be assigned explicitly after the variant's name and payload.
The value needs to be a compile-time expression.

There are restrictions to the value of a discriminant, as each variant needs to have a unique value.
Setting 2 variants to the same value will cause the compiler to emit an error.
For example:
```
enum A {
    Variant0 = 1,
    Variant1 = 1, // error: Variant1 has the same discriminant as Variant0
}

enum B {
    Variant0,
    Variant1,
    Variant2 = 1, // error: Variant 2 has the same discriminant as Variant1
}
```

A discriminant value may also not exceed the maximum value that can be stored within its type.
```
@repr(u8)
enum C {
    Val = 256, // error: discriminant value exceeds range
}
```

##### Accessing discriminant values [↵](#discriminant-)

The most common way of getting an enum's discriminant, is using the `discriminant()` utility function.

An enum can also be cast to a discriminants type, but this requires the enum to be a fieldless enum.

The last way is via a pointer cast, but this is only possible with a `@repr(C, ty)` enum, where `ty` is a know integer type.
Then this enum may be cast to a pointer of type `ty`

#### Fieldless enum [↵](#enum-types)

A fieldless enum is enum where none of its variants have a payload.
Therefore it only exists out of a discriminant, and represent a collection of uniquely named values.

In short, this meant that the enum only has:
- unit-only variants, and/or
- variant with empty payloads

#### Record enum types [↵](#enum-types)
```
<record-enum-type>           := 'record' 'enum' '{' <enum-members> '}'
<record-enum-members>        := <enum-variant> { ',' <enum-variant> } [ ',' ] { <assoc-item> }*

<record-enum-var  iant>      := <unit-enum-variant>
                             | <tuple-enum-variant>
                             | <struct-enum-variant>

<record-tuple-enum-variant>  := [ 'mut' ] <name> '(' <tuple-field> { ',' <tuple-field> }* [ ',' ] ')' [ '=' <expr> ] [ <field-tag> ]
<record-struct-enum-variant> := [ 'mut' ] <name> '{' <struct-field> { ',' <struct-field> } '}' [ '=' <expr> ] [ <field-tag> ]
```
A record enum is an enum, also known POD (Plain Old Data) enum, is a variant of an enum which is _structural_ instead of _nominal_.

Some of the most notable ones for structs are:
- all fields are public
- all fields are mutable

#### Flag enum types [↵](#enum-types)
```
<flag-enum>         := 'flag' 'enum' '{' <flag-enum-members> '}'
<flag-enum-members> := <unit-enum-variant> { ',' <unit-enum-variant> }* [ ',' ] { <assoc-item> }* 
```
A flag enum, also known as a bitset, it a variant of a fieldless enum (where only unit fields are allowed), which instead of distinct variants, store a collection of bit-flags.
Each falg enum can contain as many unique flags as are allowed by the underlying primitive type, by default, this value is represented as an `usize` value.
However, the compiler is allowed to substitute this with a smaller integer types that fits all determinants.
The discriminant is always an unsigned type.

When no explicit discriminant is given, a field will always have a value that is the next power of 2, that is greater than the previous variant.
If no explicit discriminant is set of any variant, the first variant will gain a value of 1.
If at any point the next variant's discirimnant would overflow, the compiler will emit an error.

When no variant is explicitly set to a value of 0, the flag enum will automatically as a `.None` flag with a valu of 0.

Unlike regular enums, a flag enum's explicit discriminant may reference other variant in the flag enum.

A flag will always have a set of implicitly generated methods and operators implemented to be able to work with flags.
The following function, operators and traits are implemented:

> _Todo_: Add function, operators, and traits here



[`repr` attribute]:  ../../attributes.md#repr-
[struct expression]: ../../expressions/constructing-expressions.md#struct-expressions-