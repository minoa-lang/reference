# Unsafety

Unsafe operators are operators that could potentially violate any memory-safety guaranteed provided by Minoa.

The following features cannot be used outside of an explicitly unsafe context:
- Dereferencing a [pointer](./type-system/types/pointer-types.md)
- Reading from or writing to an [extern static](./items/statics.md#external-statics-)
- Accessing a field of a [union](./type-system/types/union-types.md), with the exception of writing
- Calling any `unsafe` function (including intrinsics or extern functions)
- Implementing an [`unsafe` trait](./items/traits.md#unsafe-traits-)
- Declaring an [`extern` block](./items/external-export-block.md)