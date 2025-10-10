# Type aliases
```
<type-alias-item> := { <attribute> }* [ <vis> ] ( <type-alias> | <distinct-type-alias> | <adapt-type-alias> )
<type-alias>      := 'type' <name> [ <generic-params> ] '=' <type> [ <where-clause> ] ';'
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
> type StringHashMap(V) = HashMap(String, V);
> assert(StringHashMap(i32) == HashMap(String, i32));
> ```

> _Example_: Generic bounds
> ```
> type HashMapOfInts(K is Hash) = HashMap(K, i32);
> assert(HashMapOfInts(i32) == HashMap(i32, i32));
> 
> let _: HashMapOfInts(i32) = ... ; // ok: i32 implements Hash
> let _: HashMapOfInts(NonHashable) = ... ; // error: NonHashable does not implement `Hash`
> ```

> _Example_: Inhereted bounds
> ```
> struct Foo(T is Copy) { ... };
> 
> type Bar(T) = Foo(T);
> ```
> where `Bar(T)`, when fully expanded, will be
> ```
> type Bar(T) = Foo(T) where T is Copy;
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
<distinct-type-alias> := 'distinct' 'type' <name> [ <generic-params> ] '=' <type> <type-alias-body>
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
>     impl as From(i32) {
>         fn from(val: i32) -> Self => val as Self;
>     }
>     impl as To(i32) {
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

## Trait type aliases [↵](#type-aliases)
```
<trait-alias> := 'type' <name> [ <generic-params> ] [ <type-bounds> ] [ '=' <type> ] [ <where-clause> ] ';'
```

A trait alias declares an item that is associated with that trait's implementation.

In additon, a trait bound can also be added on the type alias.
When a trait bound is defined, it requires that any type which can be used as the associted type to implement those traits.
An implicit `Sized` trait is applied on the type alias, which can be relaxed using a `?Sized` bound.

A default type may be provided, which will be used when no explicit type alias is defined within an implementation.


[`From`]:                  #distinct-type-alias- "Todo: Link to docs"
[`To`]:                    #distinct-type-alias- "Todo: Link to docs"
[constructing expression]: ../expressions/constructing-expressions.md
[`as`]:                    ../expressions/type-cast-expressions.md
[above example code]: #