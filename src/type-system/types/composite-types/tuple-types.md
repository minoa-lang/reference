# Tuple types
```
<tuple-type> := '(' <type> { ',' <type> }+ [ ',' ] ')'
              | '(' <type> ',' ')'
              | <named-tuple>
```

A tuple type is a _structural_ type consisting out of a list of types.
They are generally consist out of a minimum of 2 sub-types, as otherwhise they can be resolved to the following types:
- 0: Unit type
- 1: Parenthesized type

A single field tuple is possible, by having the tuple consist of a single type with a trailing comma.

The resulting tuple has a number of fields, that is specified by the number of types contained within the tuple.
The number of field also defined the _arity_ of the tuple, meaning that a tuple with `n` types, is an `n`-ary tuple.
For example, a tuple with 3 types is a 3-ary tuple.

A tuple's fields can be access by their index, meaning that the first field will be `0`, the second will be `1`, etc.
Each field has the type as the one in the same position as is provided in the list of types.

Some examples of tuples:
```
(i32, ) // A 1-ary tuple of type i32
(f64, f64)
(bool, i32)
(i32, bool) // Different then the one above it
(f32, i32, ?String)
```
And some tuple-like types, which are not actually tuples:
```
() // not a tuple, but a unit type
(i32) // not a tuple either, but a parenthesized `i32`
```

An example of a usecase for a 1-ary tuple would be when planning to possibly add another type to a tuple in the future, without breaking code.
This is explained in the following pseudo-code:
```
// When starting with the following code

fn foo() -> (i32, ) { .. }
fn bar() {
    let tup = foo();
    do_something(tup.0);
}

// It is now possible to add an additional return to foo, without breaking already existing code.
fn foo() -> (i32, i64) { ... }

// bar does not need to be changed, as the access to tup.0 is still valid.
```

> _Note_: Internally, tuple types are represented as [record tuple structs], for example:
> ```
> (i32, i64, bool)
> // is represented at
> record struct __generated_name(i32, i64, bool)
> ```

> _Note_: For a nominal version of a tuple, see [tuple structs]

#### Named tuples [↵](#tuple-types)

```
<named-tuple-type> := '(' <name> ':' <type> { ',' <name> ':' <type> }+ [','] ')'
                    | '(' <name> ':' <type> ',' ')'
```

A named tuple is a variant of a tuple, where each field can have an associated name.
In addition to accessing a field by its name, a named tuple's fields can also be indexed like a regular tuple, i.e. by each element's index.

A named and regular tuple may be assigned to each other when their types match, but 2 names tuples can only be assigned to each other when both their names and types match.

Internally, tuple types are represented as [record tuple structs], for example:
```
(i32, i64, bool)
// is represented at
record struct __generated_name(i32, i64, bool)
```

> _Note_: Internally, tuple types are represented as [record tuple structs], for example:
> ```
> (a: i32, field: i64, name: bool)
> // is represented at
> record struct __generated_name(a: i32, field: i64, name: bool)
> ```



[tuple structs]:        ./tuple-struct-types.md
[record tuple structs]: ./tuple-struct-types.md#record-tuple-structs-