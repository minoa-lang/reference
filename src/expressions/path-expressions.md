# Path expressions
```
<path-expr> := [ <path-start> ] <iden>
             | 'self'
```

A path expression only represents the start of a path that refers to a local variable or an item.
All subsequent path elements will be parsed as field accesses or special index expressions in case of generics.

`self` is a special case of a path expr, as it represents the receiver of a method.

> _Example_
> ```
> // path to `a`
> b := a;
> 
> // path to `c.e`, interpreted as a path expression to `c`, followed by a field access of `e`
> e := c.d;
> 
> // `self` path
> f := self;
> ```