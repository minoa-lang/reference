# Type aliases
```
<type-alias-item> := { <attribute*> } [ <vis> ] ( <type-alias> | <new-type> | <adapt-type> )
<type-alias>      := 'type' <name> [ <generic-params> ] '=' <type> [ <where-clause> ] ';'
<new-type>        := 'distinct' 'type' <name> [ <generic-params> ] '=' <type> <new-type-body>
<adapt-type>      := 'adapt' 'type' <name> [ <generic-params> ] '=' <type> <new-type-body>
<new-type-body>   := ';'
                   | '{' <assoc-items>* '}'
```

A type alias defines a new name for an existing type, and allows for partial specialization of the generic parameters.
The 'alias type' is the new type being created, the 'aliasee' is the type that is being aliased, i.e. `type alias_type = aliasee;`.

If a generic type is passed to the aliasee, the generic in the alias type itself will gain the same bounds as those for the aliasee.

There are 3 'variants' of the type alias.

## Distinct type aliases [↵](#type-aliases)

A distinct type is a special type alias, that does not only gives a different name, etc to a type, but splits it off into a separate type, these are also known as 'newtypes.'
As the name implies, they are distinct, and cannot be cast to from the alisee.

Distinct types take over all fields and functionality of the aliasee, but can also implement additional functionality independently of the aliasee's type.

> _Note_: a limitation of this is that a disctinct type cannot acces fields that are private to the aliasee.

## Adapt type aliases [↵](#type-aliases)

An adapt type alias is similar to a distinct type, but allows the aliasee to be cast to the adapt type.

This can be used to add different interfaces to a given type, to allow a different context when they are used.

For example:
```
struct Song { ... }

adapt type SongByArtist = Song {
    impl as Cmp { ... }
}

adapt type SongByArtist = Song {
    impl as Cmp { ... }
}
```
Using this, the behavior of the type `Song` can be changed depending on which adapt type it is cast to.
An example of it's use is:
```
let songs = ...;

sort_by_key(&mut songs, fn{ (song) => song as SongByArtist });
sort_by_key(&mut songs, fn{ (song) => song as SongByTitle });
```

> _Todo:_ Figure out correct closure syntax + sort API

## Trait type aliases [↵](#type-aliases)
```
<trait-type-alias> := 'type' <name> [ <generic-params> ] [ ':' <generic-type-bounds> ] [ <where-clause> ] [ '=' <type> ] ';'
```

A trait type alias definition declared a signature for an impl type alias implementation.
They are similar to normal type aliases, but can be overwritten by an implementation.

In addition, a trait bound can also be declared on the type alias.
When a trait bound is defined, it requires any type which can be used as the associated type to implement those traits.
An implicit `Sized` trait is applied on the type alias, but can be relaxed using the `?Sized` bound.

A default type can be provided which will be used when no explicit type alias is defined within an implementation.
