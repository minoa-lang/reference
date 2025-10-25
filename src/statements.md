# Statements
```
<stmt> := <var-decl>
        | <expr-stmt>
        | <defer-stmt>
        | <errdefer-stmt>
        | <when-stmt>
```

A statement is a component of a block, representing all non-terminal elements within it, and are part of the element they are located in.
Statements differ from exprtession, in that they do not return a value and only directly exists within a scope.
