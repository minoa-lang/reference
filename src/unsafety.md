# Unsafety

Unsafe operators are operators that could potentially violate any memory-safety guaranteed provided by Minoa.

The following features cannot be used outside of an explicitly unsafe context:
- Dereferencing a [pointer]
- Reading from or writing to an [extern static]
- Accessing a field of a [union], with the exception of writing
- Calling any `unsafe` function (including intrinsics or extern functions)
- Implementing an [`unsafe` trait]
- Declaring an [`extern` block]



[`extern` block]: ./items/external-export-block.md
[extern static]:  ./items/statics.md#external-statics-
[`unsafe` trait]: ./items/traits.md#unsafe-traits-
[pointer]:        ./type-system/types/pointer-types.md
[union]:          ./type-system/types/union-types.md