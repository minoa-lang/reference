# Reference types
```
<reference-type> := `&` [ 'mut' ] <type>
```

A refernce type, similarly to a [pointer type], represents an address of a value in memory containing the underlying type.
Unlike a pointer, a reference keeps track of how it is accessed, as defined below.

References generally do not have an impact on the lifecycle of the value it points to, with the exception of temporary values.
In this case, the reference will keep the temporary value alive for the duration of the scope of the reference itself.

## Shared reference [↵](#reference-types)

Shared references prevent the direct mutation of the underlying value, where [interior mutability] can provide an exception to this rule.

The are written as `&T`.

A shared reference provides the guarantee that any other shared reference to it will not modify the underlying value.

When copying a reference, a "shallow" copy is used, meaning it only copies the pointer to the underlying data, and possibly any meta-data associated with refernces to unsized types.

> _Warning_: The guarantee mention above can only be guaranteed in 'normal' circumstances, so there are possible situation this cannot be guaranteed:
> - if the value is changed using [interior mutability]
> - if the value is modified externally, e.g. via a [pointer] or from another library providing the reference

## Mutable reference [↵](#reference-types)

Mutable references point to memory like a shared reference, but allow modification of the underlying value.
For this reason, only a single instance of a mutable reference may exist at any time.

They are written as `&mut T`.

The only way to create a mutable references is to take a mutable borrow of a value that does not have any references taken.

## Shared xor mutable [↵](#reference-types)

One of the rules references introduce for safety reasons, is that any value can only be shared or mutable at any time, and not both at the same time.
This adds a guarantee that any shared reference will contain the same underlying value whenever it is accessed, no matter what other parts of the processors are using it for.



[pointer type]:        ./pointer-types.md
[interior mutability]: ../../interior-mutability.md