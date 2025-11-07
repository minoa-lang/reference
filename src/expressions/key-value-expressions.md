# Key-value expressions
```
<key-value-expr> := <expr> '=>' <expr>
```

A key value is expression is used to generated a value with a [`KeyValue[K, V]`] type.

Key-values generally have a limit set of uses, one of the main ones being the creation of key-value array for map-like types.

> _Example_
> ```
> // `a` has type `KeyValue[i32, i32]`
> a := 3 => 4;
> ```