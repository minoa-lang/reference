# With expressions
```
<with-expr> := <expr> 'with' '{' <struct-elems> '}'
```

A `with` expression can be likened to a struct expression that always takes in a functional struct update value.
The main difference is that it can operate on anonymous types and return a new value of that type

Similary, if the left-hand operand of the `with` expression would be `expr.clone()`,
the compiler can understand this and only copy the fields that are not explicitly passed to the with expression

> _Example_
> ```
> a: struct { x, y: i32 } = .{ x: 1, y: 2 };
> 
> b := a with { y: 3 };
> // would be equivent to the following it the type of `a` could be accessed
> b := AType{ y: 3, ..a };
> 
> // only clones a.y
> c := a.clone() with { x: 0 };
> ```