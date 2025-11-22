# Full range expressions
```
<full-range-expr> := '..'
```

The `..` expression, unlike the range operators, represents an unbounded range, without a begin or end.

One of the usecases of this expression is to convert something into a slice by indexing using a full range.

> _Example_
> ```
> arr := [1, 2, 3];
> 
> // takes a full slice from the array
> slice := &arr[..];
> ```