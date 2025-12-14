# Path patterns
```
<path-pattern> := <path>
```

A path pattern matches any values which corresponds to the constant value defined by the path, or to a [struct] or [enum] with no members or variants.

What a path is allowed to point to depends on what the path represents:
- if a regular path:
  - a [struct] or [enum] type
  - a [constant]
- if a path is inferred:
  - an [enum variant]
- if the path starts with a type, or contains a trait disambiguation
  - an associated [constant]


A path pattern is refutable, except when it refers to:
- a struct
- an enum variant of an enum with only 1 variant
- a constant with a type that is irrifutable

> _Example_
> ```
> match value {
>     MAX                => (),
>     i32.MAX            => (),
>     Type.(Trait.field) => (), // refers to a constant value
>     _                  => (),
> }
> 
> match enum_value {
>     .VariantA => (),
>     .VariantB => (),
>     _         => (),
> }
> ```

# Constant patterns

When a constant `C` of type `tT` is used, it must adhere to `T is Equality`.

In addition, the constant must have structural equality, which is defined as:
- any [builtin] or [struct-like type] always have value, with the limitation that:
  - any floating point value only has structural equality if it is **not** `NaN`, this means `NaN` is not a valid value to be used in a pattern, and will result in an error
- [tuples], [arrays], and [slices] have structural equality if all their fields/elements have structural equality
- [references] have structural equality if the value they point to has structural equality
- raw [pointers] have structural equality, as long as they are first cast to a corresponding integer type, i.e. `uptr`
- Any [struct] or [enum] type which implements [`StructuralEquality`] or [`StructuralIdentity`]

The constant `C` may **not** have any references to:
- [statics] with interior mutability
- [external statics]
- [thread local statics]




[constant]:             ../items/consts.md
[statics]:              ../items/statics.md
[external statics]:     ../items/statics.md#extern-
[thread local statics]: ../items/statics.md#thread-local-starage-
[builtin]:              ../type-system/types/builtin-types.md
[enum]:                 ../type-system/types/composite-types/enum-types.md
[enum variant]:         ../type-system/types/composite-types/enum-types.md#variants-
[struct]:               ../type-system/types/composite-types/struct-types.md
[tuples]:               ../type-system/types/composite-types/tuple-types.md
[references]:           ../type-system/types/pointer-like-types/reference-types.md
[pointers]:             ../type-system/types/pointer-like-types/pointer-types.md
[string-like type]:     ../type-system/types/sequence-types/string-array-slice-types.md
[arrays]:               ../type-system/types/sequence-types/array-types.md
[slices]:               ../type-system/types/sequence-types/slice-types.md

[`StructuralEquality`]: ../operators/core-operators.md#comparison-operators- "Todo: link to docs"
[`StructuralIdentity`]: ../operators/core-operators.md#comparison-operators- "Todo: link to docs"