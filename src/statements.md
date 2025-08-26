# Statements
```
<stmt> := <var-decl>
        | <expr-stmt>
        | <defer-stmt>
        | <errdefer-stmt>
```

A statement is a component of a block, which is in turn part of an outer expression or a functions.
Statements differ from expressions, as they do not return a value and can only directly exist within a scope.
