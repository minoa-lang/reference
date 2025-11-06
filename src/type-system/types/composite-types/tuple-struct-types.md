# Tuple struct types
```
<tuple-struct>        := <type-layout-specifiers> [ 'mut' ] 'struct' '(' [ <tuple-struct-fields> ] ')' [ <tuple-stuct-body> ] ';'
<tuple-struct-fields> := <tuple-struct-fields> [ ',' <def-tuple-fields> ] [ ',' ]
                       | <def-tuple-fields> [ ',' ]
<tuple-struct-body>   := '{' { <struct-member> } '}'
```

A tuple struct type is similar to a struct type, but is somewhere in between structs and [tuples].
They can be thought of as a nominal named tuple type.

Tuple struct may also define any associated item directly, this is done in an additional body after the tuple fields.


## Fields [↵](#tuple-struct-types)
```
<tuple-struct-fields> := <tuple-struct-field> { ',' <tuple-struct-field> }*
<tuple-struct-field>  := [ <vis> ] [ 'mut' ] [ <ext-name> ':' ] <type>
```

Each field in a tuple struct is similar to that of a named tuple, meaning each field may optionally have a name.

Like struct fields, each field may also define its [visbility] and [mutability].

These fields, like [tuple] fields, may be accessed either using a [tuple index expression], or via a [field access expression] if the field is provided with a name.

Unlike a struct, it is *not* possible to declare multiple names within a single field.

### Default tuple struct fields [↵](#fields-)
```
<def-tuple-fields>    := <def-tuple-field> { ',' <def-tuple-field> }*
<def-tuple-field>     := <tuple-struct-field> '=' <expr>
```

Fields may be provided with a default value, but these must be placed at the end of the tuple, after any field that does not have a default value.
This allows these fields to be left out in a tuple struct initializer, and will instead have this defaul value assigned to it.
One major caveat with default values is that they have to be known at compile time.

This means that if a field should have a default value that is calculated at compile-time, this must either be explicitly provided in the default tuple struct initializer, or be assigned within an custom [initializer].

> _Note_: Default values for fields should not be confused with the value of field within a `Default` implementation.
>         Unlike default field values , `Default` may provide runtime calculated values and is meant to default the entirety of the structure, not just certain fields.

> _Example_: The blow structure has a compile-time known default value for the 3rd field
> ```
> struct Foo(i32, i32, i32 = 0)
> ```
> this means that it can be left out during initialization
> ```
> foo := Foo(
>     1,
>     runtime_calc(),
>     // 2 <- implied by the default value of foo.2
> ); 
> ```

## Coercion from tuple expression

Since tuple struct type have their data aranged like a tuple, it is possible to initialize or assign a tuple struct using a [tuple expression] or from another tuple.
If a named tuple is assigned, the named fields must correspond to those in the struct

> _Example_:
> ```
> struct Foo(bar: i32, baz: i32, i32);
> 
> f: Foo = (0, 1, 2);
> 
> tf: = (0, 1, 2);
> f: Foo = t;
> 
> t := (bar: 0, 1, 2);
> mut f: Foo = t;
> 
> // Also works when assign a tuple
> f = (bar: 3, 4, 5)
> ```

## Record tuple structs [↵](#tuple-struct-types)
```
<record-tuple-struct>        := <type-layout-specifiers> [ 'mut' ] 'record' [ 'struct' ] '(' [ <record-tuple-struct-fields> ] ')' [ <tuple-struct-body> ]
<record-tuple-struct-fields> := <record-tuple-struct-fields> [ ',' <record-def-tuple-fields> ] [ ',' ]
                              | <record-def-tuple-fields> [ ',' ]
<record-tuple-struct-fields> := <record-tuple-struct-field> { ',' <record-tuple-struct-field> }*
<record-tuple-struct-field>  := [ <ext-name> ':' ] <type>
<record-def-tuple-fields>    := <record-def-tuple-field> { ',' <record-def-tuple-field> }*
<record-def-tuple-field>     := <record-tuple-struct-field> '=' <expr>
```

A record tuple struct, also known as a POD (Plain Old Data) tuple struct, is a variant of a tuple struct which is a _record_ instead of _nominal_ type.
These tuple struct follow the rules defined [here](../nominal-vs-record-types.md).

For a record struct, it is allowed to leave out the `struct` keyword.



[mutability]:              ./struct-types.md#field-mutability-
[visbility]:               ./struct-types.md#field-visibility-
[tuple]:                   ./tuple-types.md
[tuples]:                  ./tuple-types.md
[field access expression]: ../../../expressions/field-access-expressions.md
[tuple index expression]:  ../../../expressions/tuple-index-expressions.md
[tuple expression]:        ../../../expressions/constructing-expressions/tuple-expressions.md