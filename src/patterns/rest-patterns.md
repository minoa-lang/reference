# Rest patterns
```
<rest-pattern> := '..'
```

A rest pattern is a variable length wildcard pattern that matches 0 or more elements, and can be used to ignore those elements.
A rest pattern does not copy, move, or borrow the values it matches.

A wildcard may only appear in the following patterns:
- [tuple patterns](./tuple-patterns.md)
- [array patterns](./slice-patterns.md)
- as an identifier's subpattern in an [slice pattern](./slice-patterns.md)

A special case of the wildcard that matches 0 or more elements, and can be used to discard any elements that are not cared about in the match.
