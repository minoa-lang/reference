# Type aliases
```
<type-alias-item> := { <attribute> }* [ <vis> ] ( <type-alias> | <distinct-type-alias> | <adapt-type-alias> )
<type-alias>      := 'type' <name> [ <generic-params> ] '=' <type> [ <type-alias-constraint> ] [ <where-clause> ] ';'
<type-alias-body> := ';'
                   | '{' { <assoc-item> }* '}'
```

A type alias defines an additional name for an existting type, and allows for partial specialization of the generic parameters.
The _alias type_ is the new type being created, and the _aliasee_ is the type being aliased, i.e. `type alias_type = aliasee;`
Both the _alias type_ and _aliasee_ are interchangable and both mean the same thing (if the additional bounds are satisfied), i.e. `assert(alias_type == aliasee)`.

> _Example_
> ```
> type Foo = i32;
> ```
> we are allowed to do the following
> ```
> fn fooify(val: i32) -> Foo => val;
> 
> let foo: Foo = 42;
> fooify(val: foo);
> ```
> We are allowed to convert between `Foo` and `i32`, as they represent the exact same type.

When generic parameters are passed from the alias type to the aliasee, the generics parameters will inhereted any bounds that are already applied to the corresponding aliasee generic parameters.

> _Example_: Default generic values
> ```
> type StringHashMap[V] = HashMap[String, V];
> assert(StringHashMap[i32] == HashMap[String, i32]);
> ```

> _Example_: Generic bounds
> ```
> type HashMapOfInts[K is Hash] = HashMap[K, i32];
> assert(HashMapOfInts[i32] == HashMap[i32, i32]);
> 
> let _: HashMapOfInts[i32] = ... ; // ok: i32 implements Hash
> let _: HashMapOfInts[NonHashable] = ... ; // error: NonHashable does not implement `Hash`
> ```

> _Example_: Inhereted bounds
> ```
> struct Foo[T is Copy] { ... };
> 
> type Bar[T] = Foo[T];
> ```
> where `Bar[T]`, when fully expanded, will be
> ```
> type Bar[T] = Foo[T] where T is Copy;
> ```

A type alias cannot be used as the name for a [constructing expression], unless an explicit initializer is provided (i.e. only for `distinct` and `adapt` type aliases).

> _Example_
> ```
> struct Foo(u32);
> 
> use Foo as UseAlias;
> type TypeAlias = Foo;
> 
> _ := Foo(3); // ok
> _ := UseAlias(3); // also ok
> _ := TypeAlias(3); // error
> ```

## Distinct type alias [↵](#type-aliases)
```
<distinct-type-alias> := 'distinct' 'type' <name> [ <generic-params> ] '=' <type> [ <type-alias-constraint> ] <type-alias-body>
```

A distinct type is similar to a regular type alias, but instead of creating an alias type which is interchangable with the aliasee, it creates a type which is distinct, which just happens to have the same internals and functionality, i.e. `assert(alias_type != aliasee)`..

It is not possible to implicitly convert between the alias type and aliasee, instead this needs to be done expliclicitly via user-provided funtionality.

Distinct type aliases may provide additional functionality to the type via any implementable associated item, the only restriction is that it cannot override one which is already implemented by the aliasee.

Within an implementation of a distinct type, the type may convert between itself and its aliasee using [`as`].
To support this externally, the distinct type must implement the relavent traits: [`From`] and [`To`].

> _Note_: A distinct type cannot access any fields or associated items that are not visibile within the scope of the distinct type alias' declaration.

> _Example_
> If we were to take the above example code, but instead define `Foo` as:
> ```
> distinct type Foo = i32;
> ```
> The sample code would result in an error, as `Foo` and `i32` are now completely different types, so we cannot implicitly convert between them.
> The above example code would need to be fixed in the following way:
> ```
> distinct type Foo = i32 {
>     fn from_i32(val: i32) => val as Foo;
>     fn(self) to_i32() -> i32 => self as i32;
> }
> 
> fn fooify(val: i32) -> Foo => Foo.from_i32(val);
> 
> let foo: Foo = Foo.from_i32(42);
> fooify(val: foo.to_i32());
> ```
> or by implementing `From` and `To`:
> ```
> distinct type Foo = i32 {
>     impl as From[i32] {
>         fn from(val: i32) -> Self => val as Self;
>     }
>     impl as To[i32] {
>         fn(self) to() -> i32 => self as i32;
>     }
> }
> fn fooify(val: i32) -> Foo => val as Foo;
> 
> let foo: Foo = 42 as Foo;
> fooify(val: foo as i32);
> ```
>
> > _Todo_: Is the implementations of `From` and `To` correct?

## Adapt type alias [↵](#type-aliases)
```
<adapt-type-alias> := 'adapt' 'type' <name> [ <generic-params> ] '=' <type> <type-alias-body>
```

An adapt type aliases is similar to a distinct type alias, but allows freely casting between it and the aliasee.
An adapt type allows additional functionality to be added when a value has the adapt alias' type.

> _Example_
> ```
> struct Song {
>     artist: &str,
>     title:  &str,
> }
> 
> adapt type SongByArtist {
>     impl as Ord {
>         fn(&self) cmp(other: &Self) -> ?Ordering => self.artist.cmp(&other.artist);
>     }
> }
> 
> adapt type SongByTitle {
>     impl as Ord {
>         fn(&self) cmp(other: &Self) -> ?Ordering => self.title.cmp(&other.title);
>     }
> }
> ```
> Using this, the behavior of sorting a series of `Song`s can now be done by choosing which adapt type.
> ```
> songs: ...;
> 
> // Sort by artist
> sort_by_key(&mut songs) { $0 as SongByArtist };
> // Sort by title
> sort_by_key(&mut songs) { $0 as SongByTitle };
> ```
>
> > _Todo_: Are the trait implementation and sort API correct?

## Type constraints

```
<type-alias-constraint>        := '@' <expr>
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

> _Note_: This is similar to having [invariant contracts] on types, but more resticted

> _Note_: This should not be confused with [constraint type aliases], which do not contain the type, but require an alias for a generic constraint

> _Note_: `@` is used as opposed to `where`, as the restriction has a similar context to pattern constraints, and the later may be confused with generics

> _Note_: In the future, the allowed constraints may be expanded, including ones depending on other fields


## Trait type aliases [↵](#type-aliases)
```
<trait-alias> := 'type' <name> [ <generic-params> ] [ <type-bounds> ] [ '=' <type> ] [ <where-clause> ] ';'
```

A trait alias declares an item that is associated with that trait's implementation.

In additon, a trait bound can also be added on the type alias.
When a trait bound is defined, it requires that any type which can be used as the associted type to implement those traits.
An implicit `Sized` trait is applied on the type alias, which can be relaxed using a `?Sized` bound.

A default type may be provided, which will be used when no explicit type alias is defined within an implementation.

## Constraint type alias [↵](#type-aliases)
```
<constraint-alias> := 'type' <name> [ <generic-params> ] [ <type-bounds> ] [ <where-clause> ] ';'
```

A constraint type alias is similar to a trait type alias, except that it doesn't allow a default type.



[constraint type aliases]:        #constraint-type-alias-
[`From`]:                         #distinct-type-alias- "Todo: Link to docs"
[`To`]:                           #distinct-type-alias- "Todo: Link to docs"
[builtin types]:                  ../builtin-types.md
[character]:                      ../builtin-types/character-types.md
[integer]:                        ../builtin-types/integer-types.md
[constructing expression]:        ../expressions/constructing-expressions.md
[floating point]:                 ../builtin-types/floating-point-types.md
[`as`]:                           ../expressions/type-cast-expressions.md
[sequence types]:                 ../sequence-types.md
[composite types]:                ../composite-types.md
[invariant contracts]:            ../../../contracts.md#invariant-contracts-
[arrays]:                         ../../../expressions/constructing-expressions/array-expressions.md
[paths]:                          ../../../expressions/path-expressions.md
[type alias]:                     ../../../items/type-aliases.md
[literals]:                       ../../../literals.md
[ranges]:                         ../../../operators/core-operators.md#range-
[identity and/or total ordering]: ../../../operators/special-operators.md#comparison-
[contains]:                       ../../../operators/special-operators.md#contains-