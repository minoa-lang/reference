# Comma expressions
```
<comma-expr> := <comma-elem> { ',' <comma-elem> }*
<comma-elem> := [ <name> ':' ] <expr>
```

Comma expressions are a set of expressions separated by commas.
It is a very niche expression type that has a very limited amount of places it can be used.

Comma expression may also includ names for each element, an example usecase of this is when returning a comma expression, where the names can be used to label the values for a named tuple.
