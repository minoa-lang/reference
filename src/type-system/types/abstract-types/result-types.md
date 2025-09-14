# Result types
```
<result-type> := <type> '!' [ <type> ]
               | '!' <type>
```

A result type allows a value to represent either a valid value or an error.

The syntax is similar to a never type, but is distinguished by having an explicit type on at least one side of it.
The value on the left represents the `T` or valid value, and the value on the right, the `E` or error value.
Meaning that a `Result(T, E)` is represented as `T!E`

When a result type (or the `Result(T, E)`) is used, the type of the value and error, the compiler is allowed to do certain optimization to encode both values in a more compact way.
For example when `E` equals to `()`, and the other value is a type like a pointer, where the 'err' state is represented with the address of `0x00000000`.

This is syntactic sugar for `Result(T, E)`, which comes with some additinal features, as the compiler understands result types. These are the following:
- `Ok(...)` can be used to set the result to a valid value, and `Err(...)` to set the result to an error, as they ar eimported via the core prelude
- when assigning a value, it can implicitly wrap a valid value with `.Some(...)`. This also works for returns
- control flow expressions have additional syntax to make using them more ergonomic
