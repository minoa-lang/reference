# Nominal vs structural types

Minoa has types that can either be nominal or structural, between these 2 kinds of types.

Both have the same type layout and mutability rules, but there are some important differences:

Nominal types:
- Nominal types do **not** implicitly implement any traits.
- Nominal types can have additional functionality and traits implemented.
- All field have configurable visibility.
- The types can be accessed directly from other scopes when 'imported'.

Structural types:
- Structural types implicitly implement a set of traits, depending on the values of the members, these are:
    - `Clone`
    - `Copy`
    - `Equality`
    - `StructuralEquality`
    - `Identity`
    - `StructuralIdentity`
    - `Hash`
    - `Debug` _TODO: this will likely the be the trait, but depends on the standard format implementation._
- Structural types do not allow any additional functionality to be implemented, as they are strictly plain data types.
- Fields cannot have explicit visibility.
- The types only exist within the scope they are defined, unless publically aliased.

> _Note_: Structural equality means that the result of an equality would be the same as checking that of all fields individually.
