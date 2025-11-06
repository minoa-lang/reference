# Vectorized types
```
<vec-type> := '[<' <expr> '>]' <type>
```

A vectorized type represents a group of values that can be operated on in parallel, using SIMD, vector instructions, or an equivalent.
They are used for explicit vectorization of code, since automatic vectorization may result in inconsistent results.

While a vectorized type looks like an array, its size defines the number of lanes on which the vectorized type will operate.
This value can be anywhere from 1 to 65'536 lanes.

The vectorized type is guaranteed to support the following element types:
- [boolean types]
- [integer types]
- [floating point types]

While there is no hard requirement that this value matches the number of lanes available for a given architecture, as the implementation will take this into account.
It is still beneficial to match the number of lanes available on a given system.

In general, it can be assumed that most operations are done element-wise, but this is not guaranteed.

Since this needs to be an abstraction that works across multiple different architectures, this type comes with the caveat that it, by default, only supports a given subset of what a system might be able to do.
If capabilities are needed that are not supported by the provided vector type, such as functionality or support for different element types, the following can be done:
- explicit intrinsics can be used within code
- a [custom vectorized type] can be provided

It is however guaranteed that all operation required by the `VectorizedType` trait are supported.

> _Note_: Naming these types 'vectorized types' instead of 'vector types' is to make clear that a vector is an explicitly different concept.
>         Assuming a SIMD type to perfectly match a mathematical vector is a common mistake, as they have certain properties which make this less than favorable.
>         More info can be seen in [this talk].

## Internal representation [↵](#vector-types)

A vector type maps to an underlying type which is parameterized by the element type and a size.

This type is defined in a seperate library and is provided to the compiler to use.
It also defines the functionality available for a vectorized type.

By default, this is provided by the [`core:vectorized`] library.

### Custom vectorized support [↵](#internal-representation-)

As it can sometimes be useful to provide a custom vector type, for example:
- when a given architecture has no vector implementation yet
- if a different implementation is needed
- if an extended set of capabilities will be provided

If a custom vectorized type is used, check its documentation to see what restrictions and functionality is provided by it.

An example of a usecase for custom vectorized type support would be GPU support.

> _Note_: Using a custom vector type requires that, to fully take advantage of this type, all libraries should be recompiled.

> _Todo_: Define the required interface + how the type is provided to the compiler




[`core:vectorized`]:      #vectorized-types "Todo: Link to library documentation"
[custom vectorized type]: #custom-vectorized-support-
[boolean types]:          ../builtin-types/boolean-types.md
[floating point types]:   ../builtin-types/floating-point-types.md
[integer types]:          ../builtin-types/integer-types.md
[this talk]:              https://deplinenoise.wordpress.com/wp-content/uploads/2015/03/gdc2015_afredriksson_simd.pdf