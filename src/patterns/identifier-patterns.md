# Identifier patterns
```
<identifier-pattern> := [ 'ref' ] [ 'mut' ] <name> [ '@' <pattern-no-top-alt> ]
```

Identifier patterns bind the value they are matched to to a variable of a given name.
This names needs to be unique within the pattern.
This binding (newly created variable) is allowed to shadow any variable that is defined before the pattern.
The scope of the binding depends on the location of where the pattern is used.

`mut` can be added to make the resulting binding mutable in code, if this is used with ut `ref`, the value will be moved into the binding.
`ref` can be added to take reference to the element being matched, instead of moving or copying it on match.
`ref` must be used instead of '&' as it is used to destructure a reference, i.e. it removes the reference from the variable.

A binding may be restricted to only bind if the value adheres to another pattern, which is located behind the name.
An identifier pattern is irrifutable if the subpattern is irrifutable, or no subpattern is provided.

By default, the variable will be copied or moved, depending on wether a value implements `Copy`.

## Binding modes [↵](#identifier-patterns-)

For ergonomics reasons, identifier patterns can operate in different binding modes depending on the context they are found in.
The binding mode is dependent on the value being matched, the pattern, and the explicitly defined binding mode.

When matching a pattern, the value being matched and the pattern are used to determine the binding mode, as defined [below](#determining-the-default-binding-move-).
To avoid the default binding mode being something other than 'move', the value being matched against can either be derefenced before matching, or have the outer pattern being a [reference pattern](./reference-patterns.md).

If no explicit `mut`, `ref`, or `ref mut` is defined, the binding uses the default mode.

The binding mode can only be explicitly set if the default binding mode is 'move'.
For example, the following are not valid, as they all have a default binding mode of 'ref':
```
let [mut a] = &[()]; // error
let [ref a] = &[()]; // error
let [ref mut a] = &[()]; // error
```
The same counts for a [reference pattern](./reference-patterns.md).
Meaning the following is also not allowed:
```
let [&a] = &[&()]; // error
```

The above example can be valid, by explicitly placing the outer slice pattern in a reference pattern, like:
```
let &[ref a] = &[()]; // This is now allowed
```

Move and reference pattern may be combined together in a single pattern.
This will result the value being partially moved, and thus cannot be used afterwards.
This only applies when the type is not copyable.

For example:
```
let Person{ name, ref age } = person;

// _ = person.name; // error: 'name' has been moved
_ = person.age; // still allowed, age has not been moved
```

### Determining the default binding move [↵](#binding-modes-)

The default mode starts by being set to 'move'.
The compiler will then go over each pattern, and follow it until it hits a binding.

To decide the current default mode, the following is done:
- If a reference value is matched to an outer non-reference, for every reference in the value:
  1. update the default binding mode
    - If the value is a mutable reference, and the default mode is 'move', update the default mode to 'mut ref'
    - If the value is a shared reference, and the default move is not 'ref', update the default mode to 'ref'
  2. dereference the value
- Otherwise, the default mode will be set to 'move'

In short, this means if the outer value being matched against contains a non-mutable `&`, the default move will be 'ref'.
If instead it does not contain a non-mutable `&` and only `&mut`, the default move will be 'ref mut'.
And otherwise it will be 'move'

> _Note_: A non-reference pattern is any pattern, with the exception of the following:
> - [bindings i.e. identifer patterns](./identifier-patterns.md)
> - [wildcard pattern](./wildcard-patterns.md)
> - [paths to constant items](./path-patterns.md) of a reference type
> - [reference patterns](./reference-patterns.md)
