# Path expressions
```
<path-expr> := [ <path-start> ] <iden>
             | 'self'
```

A path expression only represents the start of a path that refers to a local variable or item.
All path elements following this will be parsed as field accesses and method calls when generics are used.

Path expressions referencing local or static variables are place expression, all other path expressions are value expressions.

A path may also refer to an inferred path, which is represented by a `.`, followed by a name.
This is currently limited to enum members when the enum type can be inferred from the surrounding context.

`self` is a special case of a path expr, as it represents the receiver of a method.
