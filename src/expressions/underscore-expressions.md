# Underscore expressions
```
<underscore-expr> := '_'
```

Underscore expressions are used to signify a placeholder, they may be used in the following locations:
- as a placeholder in a destructuring assignment
- at the left hand side of an expression, causing it to use, but immediatelly discard the value.
- as an inferred arguments for type parameters. This requires the type to be inferrable from the context around the expression

> _Note_: that this is distinct from a wildcard pattern.
