# Key-value types
```
<key-value-type> := '(' <type> ':' <type> ')'
```

A key-balue type is used to represents a combination of a key, with an associated value.

A value of this type may be created using a [key-value expression].

## Internal representation [↵](#key-value-types)

The key-value types does not directly map to any builtin type, but is instead a special alias to the following core type:
```
struct KeyValue[K, V] {
    // ...
}
```

Meaning that `(u32 : &str)` will be converted to `KeyValue[u32, &str]`.



[key-value expression]: ../../../expressions/key-value-expressions.md