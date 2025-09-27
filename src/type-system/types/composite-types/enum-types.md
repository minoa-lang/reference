# Enum types
```
<enum-type>     := [ 'mut' ] 'enum' [ <discriminant> ] '{' <enum-members> '}'
<enum-memebers> := [ <enum-variants> ] { <assoc-item> }*
```

An enum (enumeration) type is a collection of distinct values, called variants, which are disambiguated using a discriminant.
If any of the fields of an enum include a payload, the enum is also known as an ADT (Algebreic Data Type).

Enums play particular well together with pattern matching.

## Anonymous enums [↵](#enum-types)

An anonymous enum is what is produced when an explicit enum type is used.
These structs cannot be manually referenced, and therefore have a limited use.

Some usecases of an anonymous enum:
- to be assigned to type alias
- to be returned from a [meta function] that returns a type
- as a field of another type, allowing enum-like field access, but not have the type field explicitly usable.
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
>    - Otherwise, the name will be a generic name: `__anon_enum`
> 2. After this information about its span will be appended, which at minimum will contain the line and column of where the struct is defined.
>    In case this is defined within a meta function, the line and column will be taken from the location it is invoked
> 3. A pseudo-random value will be appened to the end to avoid any other name conflicts (this is not guaranteed to be stable across compilations).
>
> A compiler can enable truly random names for a enum, which will result in the enum getting a name of `__ae`, followed by a psuedo-random number.

## Variants [↵](#enum-types)
```
<enum-variants> := <enum-variant> { ',' <enum-variant> }* [ ',' <non-exhaustive-enum-variant> ] [ ',' ]
<enum-variant>  := <unit-enum-variant>
                 | <tuple-enum-variant>
                 | <struct-enum-variant>
```

An enum variant is one of the values that an enum can have.
Each variant may include additional data, which is called the variant's payload.

Each variant has its own 'constuctor', which is separate from any initializers defined on the type.
These are accessed differently depending on the type of variant.

Each enum requires at least a single variant to be supplied.


Each field in a variant may also have a field tag associated with it.

### Unit variants [↵](#variants-) 
```
<unit-enum-variant> := <ext-name> [ '=' <expr> ] [ <field-tag> ]
```

A unit variant is a variant that does not include any additional data, and thus does not have any additonal impact on the size of the enum.

This variant can be 'constructed' using a [path expression].

> _Example_
> ```
> enum Foo {
>     Bar,
> }
> ```

### Tuple variants [↵](#variants-) 
```
<tuple-enum-variant> := [ 'mut' ] <ext-name> '(' <named-tuple-fields> ')' [ '=' <expr> ] [ <field-tag> ]
```

A tuple variant contains a [tuple struct] as additional data.

This variant can be constructed using a [call expression], similarly to a tuple struct, using a path to the variant.

> _Example_
> ```
> enum Foo {
>     Bar(i32, named: u32),
> }
> ```

### Struct variants [↵](#variants-) 
```
<struct-enum-variant> := [ 'mut' ] <ext-name> '{' <struct-fields> '}' [ '=' <expr> ] [ <field-tag> ]
```

A struct enum variant contains a [struct] as additional data.

This variant can be constructed using a [struct expression], similarly to a struct, using a path to the variant.

> _Example_
> ```
> enum Foo {
>     Bar {
>         a: i32,
>         b: u32,
>     },
> }
> ```

### Non-exhautive variant [↵](#variants-)
```
<non-exhaustive-enum-variant> := '_'
```

A non-exhaustive variant can be added at the end of the enum, indicating that this enum can not be exhaustively checked when matching.
This can be used to ensure any pattern matching done on the enum will continue to work, even if a variant is added, since a default case is always needed.

This variant cannot be manually accessed, and does not affect the the discriminant, as it cannot be represented.

> _Example_
> ```
> enum Foo {
>     A,
>     B,
>     _,
> }
> 
> let f = Foo.A;
> 
> match f {
>     .A => (),
>     .B => (),
> 
>     // Commenting this will result in an error, as the pattern matching is not exhaustive
>     _ => ()
> }
> ```

### Variant mutability [↵](#variants-)

Each variant and field within it are immutable by derfault, meaning they can only be set when the enum is intialized.
A variant or fields may be explicitly declared as mutable, allowing its value to be changed in other locations.

The mutability of a variant can be defined at 3 levels:
- directly on the fields of a variant
- on a specific variant
- on the enum as a whole

This mutability will 'trickle down' to all contained variant and fields, meaning that making a variant `mut` will make all its fields `mut`, same with the entire enum being `mut` making all variants also `mut`.

> _Example_: Mutable variants
> ```
> enum Foo {
>     mut A {
>         a: i32,
>         b: u32,
>     },
>     B {
>         c: f32
>     }
> }
> ```
> will be converted to
> ```
> enum Foo {
>     A {
>         mut a: i32,
>         mut b: u32,
>     },
>     B {
>         c: f32
>     }
> }
> ```

> _Example_: Mutable enum
> ```
> mut enum Foo {
>     A {
>         a: i32,
>         b: u32,
>     },
>     B {
>         c: f32
>     }
> }
> ```
> will be converted to
> ```
> enum Foo {
>     A {
>         mut a: i32,
>         mut b: u32,
>     },
>     B {
>         mut c: f32
>     }
> }
> ```

### Variant visibility [↵](#variants-)

The visibility of an enum, its variants, and its fields are shared.
It may only be declared on the enum itself.

> _Example_:
> ```
> pub enum Foo {
>     A {
>         a: i32,
>         b: u32,
>     }
>     B,
> }
> ```

## Discriminant [↵](#enum-types)
```
<discriminant> := ':' <type>
``` 

Each value is represented using its discriminant, this is a value that logically associated as enum instance with the varaint it holds.

By default, this value will be represented as an `isize` value.
However, the compiler is allowed to substitute this with a smaller integer type that first all determinants, including unsigned integers if no distriminant needs to hold a negative value.
This is done to reduce the amount of memory that is taken in by each enum instance.

The value of a discriminant may also not exceed the maximum storable value of the discriminant type.

> _Note_: The compiler may only subsititue the `isize` with one of the following: `i8`,`i16`,`i32`,`i64`,`u8`,`u16`,`u32`,`u64`

> _Example_: The compiler can derive a smaller type for the discriminant of the following enum
> ```
> enum Foo {
>     A,
>     B,
> }
> ```
> Since the enum can only contain a 0 or 1 value, and does not go negative, the `isize` is internally stored as a `u8`

### Assigning discriminant values [↵](#discriminant-)

By default, the discriminant is implicltly assigned, where each variant will take on the discriminant of the previous value, plus one.
If the initial variant has no value assigned, it will get a value of 0.

Discriminatn can also be explicltly assigned to a variant using a compile-time expression.

> _Example_
> ```
> enum Foo {
>     A = 1,
>     B = 3
> }
> ```


> _Example_: Auto-increment for non-explictly assigned values
> ```
> enum Foo {
>     A,
>     B,
>     C = 8,
>     D,
>     E = 3,
>     F
> }
> 
> assert(Foo.A as isize == 0);
> assert(Foo.B as isize == 1);
> assert(Foo.C as isize == 8);
> assert(Foo.D as isize == 9);
> assert(Foo.E as isize == 3);
> assert(Foo.F as isize == 4);
> ```

### Accessing discriminant values [↵](#discriminant-)

A discriminant's value can be accessed using the [`discriminant(enum_type)`] function.

> _Example_
> ```
> enum Foo {
>     A,
>     B(i32),
> }
> 
> assert(discriminant(Foo.A) == 0);
> assert(discriminant(Foo.B) == 1);
> ```
> _Todo_: Fix up function path


If the discriminant is a fieldless enum, the enum may also be directly cast to the disriminant's type and be accessed that way.

> _Example_
> ```
> enum Foo {
>     A,
>     B,
> }
> 
> assert(Foo.A as usize == 0);
> assert(Foo.B as usize == 1);
> ```

In addition, if the enum has no external discriminant, and the `@repr(C)` attribute applied, and an explicit discriminant type specified, then it is allowed to convert a pointer to the enum to a pointer of the disriminant type.
This pointer can then be used to directly access the discriminant's value.
Modifying this value is [illegal behavior](../../../illegal-behavior.md#overwriting-an-enums-discriminant).

> _Example_
> ```
> @repr(C)
> enum Foo : u8 {
>     A,
>     B(i32),
> 
> 
>     fn(&self) discriminant() {
>         unsafe { (&Foo as ^Self as ^u8)^ }
>     }
> }
> 
> assert(Foo.A.discriminant() == 0);
> assert(Foo.B.discriminant() == 1);
> ```

#### Restrictions [↵](#assigning-discriminant-values-)

Unlike only unsing auto-increment, assigning any discriminant value manually may cause overlapping values.
If any discriminant ends up overlapping and other variant's discriminant, it will generate an error.

> _Example_:
> ```
> enum Foo {
>     A = 1,
>     // error: `A` has already been assigned the discriminant value `2`
>     B = 1,
> }
> ```
> This also happens when a field that was assigned by an auto-increment produces a conflicting value.
> ```
> enum Foo {
>     A = 1,
>     B,
> 
>     // error: `B` has already been assigned the discriminant value `2`
>     C = 2,
> }
> ```

### Custom discriminant types [↵](#discriminant-)

In addition, it is also possible to define the discriminant explicitly.
To use a custom type, it needs to adhere to the following constaints:
- The value needs to be able to exists at compile-time
- The value needs to implement the [identity comparison operator].

> _Example_: An enum with a string discriminant
> ```
> enum Foo: &str {
>     A = "A",
>     B = "Other",
> }
> ```

A custom discriminant type also disables the auto-incrementing of indices by default.
This can be done by having the discriminant type implement the `DiscriminantInc` trait.

If an auto-incrementing discriminant is not used, the type can also generate the discriminant based on the enum field.
This is done by adding the `@discriminant_gen(meta)` attribute.
This attribute is used to tell the compile to pass the varaint into the supplied [meta function].

It can be applied either directly on the enum, or on the discriminant type.
An attribute applied on an enum has priority to one defined on the 

Additionally, the following table contain a list of types which have default values

Type      | Initial value | Increment | Auto-assign
----------|---------------|-----------|--------------------------------
`i{N}`    | `0`           | `+ 1`     | n/a
`u{N}`    | `0`           | `+ 1`     | n/a
`f{N}`    | `0.0`         | `+ 1.0`   | n/a
`char{N}` | n/a           | n/a       | First character of field name
`&str`    | n/a           | n/a       | Field name

> _Note_: The `@discriminant_gen(meta)` attribute has priority over using auto-incrementing

### External discriminant [↵](#discriminant-)
```
<enum-ext-type> := ( <enum-type> | <path-type> ) 'in' <expr>
```

Enums also allow the discriminant to be stored externally to the enum.
No additional syntax is needed for this, as in the background, any operation using the discriminant function handles both integrated and external discriminant values.

This value always needs to contain a valid value for the enum, or this will result in [illegal behavior](../../../illegal-behavior.md#external-enum-discriminant).

> _Example_:
> ```
> enum Foo: i32 {
>     A(i32),
>     B(u32),
> }
> 
> d := 0;
> let f: Foo in d = .B(0);
> 
> assert(d == 1);
> 
> // illegal behavior: discriminant does not contain a valid value
> // match f {
> //     _ => ()
> // }
> ```

## Fieldless enums [↵](#enum-types)

A fieldless enum is an enum where none of its variants have a payload.
It therefore exists out of only a discriminant and just representa a collection of uniquely named values.

### Iterating over variants [↵](#fieldless-enums-)

A fieldless enum is able to iterate over all variants contained in the enum.

While normally, iterating over a type itself is not possible, in this case, an iterator will be created which goes over all variants within the enum.

> _Example_
> ```
> enum Foo {
>     A,
>     B,
>     C,
> }
> 
> for variant in Foo {
>     // Iterates over .A, .B, and .C
> }
> ```

### Initializing from a raw value [↵](#fieldless-enums-)

A fieldless enum can also be created directly from a raw value.
This is done by an implicitly supplied initializer.

_Example_
```
enum Foo {
    A,
    B,
    C,
}

f := Foo(raw_value: 1);
assert(f == Foo.A);
```

## Record enums [↵](#enum-types)
```
<record-enum-type> := 'record' 'enum' '{' <enum-members> '}'
```

A record enum is a variant of an enum which is _structural_ instead of _nominal_.
These structs follow the rules defined [here](../nominal-vs-structural-types.md).

Some of the most notable ones for structs are:
- all fields are public
- all fields are mutable

## Adhoc ADT enums [↵](#enum-types)
```
<adhoc-enum> := <type> { '|' <type> }+
```

An adhoc ADT enum type is a special variant of an enum which can contain multiple types, but cannot be explicitly defined.
None of its variants has a name, and must therefore be distinguished using either a [type check expression] or a [type check pattern].

> _Example_
> ```
> let a: i32 | f32 = 1.0;
> 
> match a {
>     val @ is i32 => do_int(val),
>     val @ is f32 => do_float(val),
> }
> ```
> > _Todo_: Is there a shorthand for type check patterns?

If a literal is assigned to an adhoc enum, type confusion may happen if the literal can be converted to 2 or more possible types, in this case, the user needs to be explicit about the type of the literal
```
// error: cannot distinguish between either type
// let a: f32 | f64 = 1.0;

// Explicitly defined to be a f32, so this works fine
let a: f32 | f64 = 1.0f32;
```

> _Implementation note_: Internally, adhoc enums generate a full enum, and any check check is converted into a discriminant comparison.

## Enum field coercion [↵](#enum-types)

When assigning a value to an enum, some may be coerced directly to an enum.
This requires the enum to be explicitly marked to support coercion using `@enum_coerce`.

This happens when a value uniquely matches the payload of a enum variant is assigned.
If a tuple variant only contains a single value, the value can be directly assigned without needing to wrap it in any enum.

_Example_
```
@enum_coerce
enum Foo {
    A(i32),
    B(f32),
    C(f64),
    D{
        a: i32,
        b: i32,
    }
}

let f: Foo = 0;
// Gets converted to
let f: Foo = Foo.A(0);

// error: cannot distinguish between `.B` or `.C`, needs an explicit disambiguation
let f: Foo = 1.0;

```

> _Tooling_: Tools should make clear a coercion happens here, and optionally annotate to which variant assignment happens.

## Flag enums [↵](#enum-types)
```
<flag-enum-type> := 'enum' [ <discriminant> ] '{' <flag-enum-members> '}'
<flag-enum-members> := <unit-enum-variant> { ',' <unit-enum-variant> } [ ',' ] { <assoc-item> }*
```

A flag enum, also known as a bitset, is a variant of a fieldless enum which instead of distinct values, stores a set of bit-flags.

Each flag enum can contain as many unique flags as are allowed by the discriminator type, by default this type is representated as a `usize` value.
However, similarly to regular enums, the compiler is allowed to substitute this with a small unsigned integer type that fits all the flags.

> _Note_: The compiler may only subsititue the `usize` with one of the following: `u8`,`u16`,`u32`,`u64`

The user is allowed to change the discriminant to any unsigned integer type, other type are not supported.

When no explicit discriminant is assigned to a variant, it will assign the following:
- if it is the first value, it will get a value of 1
- each subsequent variant will contain the next power of 2

If adding a flag would make the discriminant overflow, a compiler error will be generated.

If no explicit `0` flag is defined, the compiler will automatically generate a `.None` field with a value of 0.

Unlike other enums, a flag enum may use it's flags directly in the calculation of any other discriminant.

A flag enum will always have a set of implicitly generated methods and operators implement on it.
These are the following:
- traits:
- methods:
- operators:

> _Todo_: Add traits, methods, and operators here

> _Todo_: Could add support to be able to access individual flags by name as a boolean value, i.e. `value.FlagName` or `value.FlagName = true`



[`discriminant(enum_type)`]:    #accessing-discriminant-values- "Todo: link to docs"
[struct]:                       ./struct-types.md
[tuple struct]:                 ./tuple-struct-types.md
[path expression]:              ../../../expressions/path-expressions.md
[call expression]:              ../../../expressions/call-expressions.md
[struct expression]:            ../../../expressions/constructing-expressions/struct-expressions.md
[type check expression]:        ../../../expressions/type-check-expressions.md
[illegal behavior]:             ../../../illegal-behavior.md#overwriting-an-enums-discriminant
[meta function]:                ../../../metaprogramming.md
[identity comparison operator]: ../../../operators/core-operators.md#identity
[type check pattern]:           ../../../patterns/type-check-patterns.md

[`repr` attribute]:  ../../../attributes/abi-link-symbol-ffi.md#repr-