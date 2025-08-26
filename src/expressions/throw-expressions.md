# Throw expressions
```
<throw-expr> := `throw` <expr>
```

Throw can be used to return an erronous value from a function returning an erronous-supporting type, and also evaluate all in-scope [defer-on-error statements](../statements/defer-statements.md#defer-on-error-statement-).

Unlike languages with exception, this expression can be seen as a 'fancy' return expression returning an erronous value, thus _not_ causing any unclear control flow.
