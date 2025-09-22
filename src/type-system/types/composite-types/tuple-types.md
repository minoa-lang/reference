# Tuple types
```
<tuple-type> := '(' <type> { ',' <type> }+ [ ',' ] ')'
              | '(' <type> ',' ')'
              | <named-tuple>
```

A tuple type is a _structural_ type consisting out of a list of types.
They generally consist out of a minimum of 2 sub-types, with the exception of a special 1-ary tuple.

Otherwise these are interepreted as different types based on the number of types provided.
- 0 types: [unit type]
- 1 types: [parenthesized type]

A single field tuple is possible, by having the tuple consist of a single type with a trailing comma.

The resulting tuple has a number of fields, that is specified by the number of types contained within the tuple.
The number of field also defines the _arity_ of the tuple, meaning that a tuple with `n` types, is an `n`-ary tuple, e.g. a tuple with 3 types is a 3-ary tuple.
A tuple is limited to 65'536 fields.

A tuple's fields can be access by their index using a [tuple index expression], meaning that the first field will be `0`, the second will be `1`, etc.
Each field has the type that is provided by the type in the same position as the field.

> _Example_:
> ```
> (i32, ) // A 1-ary tuple of type i32
> (f64, f64)
> (bool, i32)
> (i32, bool) // Different then the one above it
> (f32, i32, ?String)
> ```
> And some tuple-like types, which are not actually tuples:
> ```
> () // not a tuple, but a unit type
> (i32) // not a tuple either, but a parenthesized `i32`
> ```


> _Example_: A usecase for a 1-ary tuple would be when planning to possibly add another type to a tuple in the future, without breaking code.
> This is explained in the following pseudo-code:
> ```
> // When starting with the following code
> 
> fn foo() -> (i32, ) { .. }
> fn bar() {
>     let tup = foo();
>     do_something(tup.0);
> }
> 
> // It is now possible to add an additional return to foo, without breaking already existing code.
> fn foo() -> (i32, i64) { ... }
> 
> // bar does not need to be changed, as the access to tup.0 is still valid.
> ```

> _Note_: Internally, tuple types are represented as special [record tuple structs], for example:
> ```
> (i32, i64, bool)
> // is represented at
> record struct __generated_name(i32, i64, bool)
> ```

> _Note_: For a nominal version of a tuple, see [tuple structs]

## Named tuples [↵](#tuple-types)
```
<named-tuple-type>  := '(' <named-tuple-field> ',' <named-tuple-fields> ')'
                     | '(' <ext-name> ':' <type> ',' ')'
<named-tuple-fields> := <named-tuple-field> { ',' <named-tuple-field> }*
<named-tuple-field>  := [ <ext-name> ':' ] <type>
```

A named tuple is a variant of a tuple, where each field can have an associated name.
In addition to accessing a field using a [tuple index expression], it can also be accessed using a [field access expression] if the field is provided with a name.
In addition to accessing a field by its name, a named tuple's fields can also be indexed like a regular tuple, i.e. by each element's index.

A named and regular tuple may be assigned to each other when their types match, but 2 named tuples can only be assigned to each other when both their names and types match.

It is not required for all fields to be named, so a mix of named and unnamed fields may be used, e.g. `(named: i32, i32)`.

> _Note_: Internally, tuple types are represented as special [record tuple structs], for example:
> ```
> (a: i32, field: i64, name: bool)
> // is represented at
> record struct __generated_name(a: i32, field: i64, name: bool)
> ```

> _Reasoning_: Named tuple exists as an alternative to [record tuple structs], some uses of them are the following:
> - Add a convenience allowing a regular tuple to be directly assigned to a named tuple, or in the reverse order
> - Unlike record tuples, they can be created directly inside of a [tuple expression] without needing to declare an explicit type.

## Tuple coercion

Tuples can be coerced to each other as long as their types match.
In addition for named tuple, any tuple can be coerced to a named tuple as long as the following rules are followed:
- the type of the fields is the same
- either of:
  - the name of a field match with the resulting field
  - the field being moved from does not have a name

_Example_:
```
a := (x: 0, y: 1, z: 2);

// Fine, moving from tuple that has no names defined
a = (3, 4, 5);

// Fine, moving from a tuple that either has matching names, or has no names defined
a = (x: 6, 7, z: 9);

// error: fields do not match names
// a = (x: 10, z: 11, 12);
```



[tuple structs]:           ./tuple-struct-types.md
[record tuple structs]:    ./tuple-struct-types.md#record-tuple-structs-
[unit type]:               ../builtin-types/unit-types.md
[parenthesized type]:      ../../types.md#parenthesized-types-
[tuple index expression]:  ../../../expressions/tuple-index-expressions.md
[tuple expression]:        ../../../expressions/constructing-expressions.md#tuple-expression-
[field access expression]: ../../../expressions/field-access-expressions.md