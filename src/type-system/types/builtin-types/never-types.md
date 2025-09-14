# Never type
```
<never-type> := '!'
```

The never type is a special type that represents an operation that can never complete.
When used in type inferrence, his type can be implicitly coerced into any type.

The never type is only valid when used as the return value of a function, indicating that it will never return.
This also means that it cannot be used as part of a [compound type].

Never types cannot be manually created.



[compound type]: ../../types.md#compound-types-