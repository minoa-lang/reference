# Identifiers & Paths

Names, identifiers, and paths are used to refer to things like:
- types
- items
- generic paramters
- variable bindings
- loop labels
- fields
- attributes
- etc.

## Identifiers [↵](#identifiers--paths)

```
<iden-name> := <name> | <path-disambig>
<iden>      := <iden-name> [ <generic-args> ]
```

An identifier is a sub-segment of a path, which consists out of a name and optional generic arguments.
Identifiers refer to a single element in a path which can be uniquely identified by its name and generics.

### Trait disambiguation [↵](#identifiers-)

```
<path-disambig> := '(' <trait-path> '.' <name> ')'
```

Sometimes an identifier can not be resolved directly to a trait's item, this can be caused by the item either not being from an `extend` trait impl, or when at least 2 `extend` trait impl items exists that have the same name, but no explicit (non-trait) item exists on the previous item in the path.
This can be resolved by explicitly prepending a path to the implemented trait that has the desired item to be accessed.
The trait path may not end in a function-style trait path end.

## Paths [↵](#identifiers--paths)

A path is a sequence of one or more identifiers, logically separated by a `.`.
If a path consists out of only one segment, and refers to either an item or variable in the local scope.
If a path has multiple parameters, it refers to an item.

Two examples are:
```
x;
x.y.z;
```

There are multiple types of paths:

### Simple paths [↵](#paths-)

```
<simple-path> := [<simple-path-start>] { '.' <simple-path-segment> }*
<simple-path-start> := '.' | 'super' | 'self'
<simple-path-segment> := <name> | <name>
```

Simple path are used for visitility, attributes, metafunctions and use items.

### Regular paths [↵](#paths-)
```
<path> := [ <path-start> ] <iden> { '.' <iden> }*
```

Paths are used in expression and types to refer to a given item.
They exists out of an optional path start, followed by a sequence of identifiers.

When a path is used in an expression, they are essentially a sequence of field accesses and method calls.
The only part of the path that will be uniquely identified as a path is the path start and the first identifier (without generics).

#### Path start [↵](#regular-paths-)

```
<path-start>           := <path-type-start> | <path-self-type-start> | <path-infer-start>
<path-type-start>      := '(' <type> ')' '.'
<path-self-type-start> := 'Self' '.'
<path-infer-start>     := '.' 
```
Path may start with a 1 of 4 different special starts:
- `(type).`: This allows a path to get an associated item out of a type.
- `Self.`: This allows a path to be relative to the current type being implemented. If the type is implementing a trait, the first element will be automatically disambiguated with the trait currently being implemented.
- `.`: This allows the preceeeding path to be inferred from the surrounding context of the path, if the preceeding path cannot be inferred, a compiler error will be emitted.
- `:.`: This allows a path to be directly relative the the current library, ignoring any other modules/scope it is found it, i.e. it looks in the root namespace of the current library.

### Trait paths [↵](#paths-)

```
<trait-path-fn> := <name> '(' <fn-type-params> ')' [ '->' <type-no-bounds> ]
<trait-path> := [ <path-start> ] <type-path-segment> { '.' <type-path-segment> }* [ '.' <trait-path-fn> ]
              |  <trait-path-fn>
```

Trait paths are a special variation of paths that are used in any location a trait is explicitly expected.
A trait path may end in a special function end, the usecase for is limited to the for function call related traits, allowing the parameters and return type for these to be specified.
