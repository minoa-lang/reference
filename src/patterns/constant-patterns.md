# Constant patterns

When a constant `C` of type `T` is used, it must adhere to `T is Equality`.

In addition, the constant must have structural equality, which is defined as:
- Any [primitive](../type-system/types/primitive-types.md) or [string-slice type](../type-system/types/string-slice-type.md), in addition
  - Any floating point value only has structural equality when it is **not** `NaN`, this means `NaN` is not a valid value to be used in pattern, and will generate a compiler error
- Tuples, arrays, and slices have strucutral equality if all their fields/elements have structural equality
- References have structural euqality if the value they point to has structural equality
- Raw pointers have structural equality, if it was defined as a constant integer (and then cast/transmuted)
- Any `struct` or `enum` type which implements `StructuralIdentity` or `StructuralEquality`
