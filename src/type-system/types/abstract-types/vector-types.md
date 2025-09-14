# Vector types
```
<vec-type> := '[<' <expr> '>]' <type>
```

A vector type represents a group of values that are operated on in parallel, using SIMD where possible.
Vectors consists out of multiple underlying types, each with a predetermined size.

They generally support the same core operators that their base types support.
Operations on vectors are generally done element-wise, but this is not guaranteed, check the relavent function for more info about this.

> _Note_: By default, only primitive types are supported, check `Vector`'s implementation for more details.

#### Custom vector support [↵](#11128-vector-types-)

Custom vector support can be added by specializing the `Vector` type, using:
```
#specialize_vector(ty, n) {
    ...
}
```
This both allows functionality to be implemented, and notifies the compiler of the newly supported type.

If needed the underlying implementation may also be replaced by specifying a type that matches that of the vector implementation, e.g.:
```
struct Vec(T is SupportedVecType, N: usize) where
    is_vec_size_supported(N)
{
    ...
}
```
If a custom vector type is used, the `#specialize_vector` metafunction will applied to that type.

An example of a specialized vector would be GPU vector support.

> _Note_: Defining a custom vector implementation requires the library with the implementation be provided to the compiler and the `std` lib must be compiled from scratch

> _Todo_: Is this the best way of doing this?
