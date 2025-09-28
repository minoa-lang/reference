# Nominal vs record types

Minoa has type that can either be nominal or record types.

Both have the same layout and mutability rules, but there are some important differences

## Nominal [↵](#nominal-vs-record-types)

Nominal type have the following properties:
- nominal type do **not** implicitly implement any trait, other than [auto-traits]
- all fields have configurable visibility

## Record [↵](#nominal-vs-record-types)

Record types have the following properties:
- Cannot be `?Sized`, always is `Sized`
- Record types automatically implement traits marked with `@record_trait`, including the following `core` traits:
  - `Clone`
  - `Copy`
  - `Equality`
  - `StructuralEquality`
  - `Identity`
  - `StructurealIdentity`
  - `Hash`
- all fields are public
- when initialized using a struct expression, any fields left out will be assigned their `Default::default()` value.



[auto-traits]: ../items/traits.md "Todo: fix segment"