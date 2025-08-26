# Recursive types

Nominal or ordinal types may be recursive, meaning that a type may have member that refers, directly or indirectly, to the current type.
These are some limiations on how types can be nested:
- Type aliases must include a nominal or structural type in the recursion, meaning type aliases, or other types like arrays and tuples are not allowed.
  i.e. `type Foo = &[Foo]` is not allowed.
- The size of a recursive type must be finite, meaning that the recursive field must be 'broken up' by a type like a pointer or reference type.
