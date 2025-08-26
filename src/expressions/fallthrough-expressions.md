# Fallthrough expressions
```
<fallthrough-expr> := 'fallthrough' [ <label> ]
```

When a `fallthrough` is encountered, the current arm of a `match` will immediatelly terminate and the arm next arm will be evaluated.
If a label is given, the arm associated with the label will be evaluated instead.
