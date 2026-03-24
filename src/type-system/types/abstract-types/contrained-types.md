# Constrained types
```
<constrained-type>       := <type> <type-constraint>
<type-constraint>        := '@' <expr>
                          | '@' <type-member-constraint> { '&&' <type-member-constraint> }*
<type-member-constraint> := <type-constraint-member> <type-constraint-op> <expr>    
<type-constraint-member> := '[' <expr> ']'
                          | '.' ( <ext-name> | <dec-int-literal> )
                          | '.' <ext-name> '(' '.' <dec-int-literal> { '&&' '.' <dec-int-literal> }* ')'
                          | '.' <ext-name> '{' '.' <ext-name> { '&&' '.' <ext-name> }* '}'
<type-constraint-op>     := '=='
                          | '<'
                          | '<='
                          | '>'
                          | '>='
                          | 'in'
                          | '!in'
```

A constrained type allows a given set of restrictions to type.
Specifically, it allows a restriction or constraint to be added to any type or sub-type which may be represented as a type which can
- be compared to another value, by implementing the [identity and/or total ordering] operators
- can be within a range or collection, by implementing the [contains] operator

The allowed contraints depend on the type they're applied on:
- [builtin types] may be limited to a given range of values, the following are supported:
  - [integer]
  - [floating point]
  - [character]

  > _Example_
  >
  > Restricts `A` to be an i32 which may only hold -1, 0, or 1.
  > ```
  > type A = i32 @ -1..=1;
  > ```
  > 
  > Or we can restrict a type to only a single value
  > ```
  > type One = i32 @ 1;
  > ```
- [sequence types] may limit the value of a given range or elements
  
  > _Example_
  > 
  > Limit the first element to only contain a range of values, and the last to only 1 valid value
  > ```
  > type A = [3]i32 @ [0] in 0..=4 && [2] in [42, 1337];
  > ```

- [composite types] may limit the value of a given field

  > _Example_
  > 
  > Limiting a named field
  > ```
  > struct Foo {
  >     a: i32,
  >     b: i32,
  > }
  > 
  > type A = Foo @ .a in -1..=1;
  > ```
  > 
  > limiting a tuple field
  > ```
  > type B = (i32, f32) @ .1 >= 0.0;
  > ```
  > 
  > limiting enum variants
  > ```
  > struct Bar {
  >     Val0,
  >     Val1(i32, f32),
  >     Val2{ x, y: f32 }
  > }
  > 
  > type C = Bar @ .Val1(.2) >= 0 && .Val2{.x && .y} in -1.0..=1.0;
  > ```


The constraint checks are limited to the following expressions:
- [literals], for all operators, except `in` and `!in`
- [ranges] and [arrays], for `in` and `!in`
- [paths] to any of the above

Constrained types are limited to be within the following locations:
- [parenthesized type]
- [type alias] 

> _Note_: This is similar to having [invariant contracts] on types, but more resticted

> _Note_: Constrained type should not be confused with generic [value bounds] or [constraints], as those are compile-time constraints

> _Note_: `@` is used as opposed to `where`, as the restriction has a similar context to pattern constraints, and the later may be confused with generics

> _Note_: In the future, the allowed constraints may be expanded, including ones depending on other fields



[builtin types]:                  ../builtin-types.md
[character]:                      ../builtin-types/character-types.md
[integer]:                        ../builtin-types/integer-types.md
[floating point]:                 ../builtin-types/floating-point-types.md
[sequence types]:                 ../sequence-types.md
[composite types]:                ../composite-types.md
[parenthesized type]:             ../../types.md#parenthesized-types-
[invariant contracts]:            ../../../contracts.md#invariant-contracts-
[arrays]:                         ../../../expressions/constructing-expressions/array-expressions.md
[paths]:                          ../../../expressions/path-expressions.md
[value bounds]:                   ../../../generics.md#value-bounds-
[constraints]:                    ../../../generics/constraints.md
[type alias]:                     ../../../items/type-aliases.md
[literals]:                       ../../../literals.md
[ranges]:                         ../../../operators/core-operators.md#range-
[identity and/or total ordering]: ../../../operators/special-operators.md#comparison-
[contains]:                       ../../../operators/special-operators.md#contains-