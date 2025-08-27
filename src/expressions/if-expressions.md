# If expressions
```
<if-expr>          := <label> [ 'const' ] 'if' <branch-condition> <block> ( 'else' <block> )?
                    | <err-if-expr>
<branch-condition> := ( <expr> | <cond-let-binding> ) ( '&&' ( <expr> | <cond-let-binding> ) )*
```

An `if` expression is a conditional branch in program control.

An if expression will execute its subsequent block when the condition is `true`, and will skip all other blocks.
Otherwise when an `else` is present, it will be executed.
This happens for each if statement if they are chained, until a block is executed.

By default, all if statements evaluate to a value of `()`.
When any branch returns a value, all possible branches should return the value a value with the same type.

An if-statement can be explicilty marked as `const`, guaranteeing it will execute at compile time, which essentially eliminates the if-statement.
If just a constant expressions is supplied, the compiler may eliminate the if, but there is no guarantee it will.

## Branch conditions & `let` bindings [↵](#if-expression)
```
<branch-condition> := <condition-elem> { '&&' <condition-elem> }*
<condition-elem>   := <expr> | <opt-shorthand> | <let-binding>
```

A branch condition is any expression that evaluates to a boolean expression.

Both the `&&` and `||` operator have special meaning in a condition, depending on the condition itself.
If any `let` binding or optional shorthand is used, they have the following properties:
- `&&`: any subsequent condition has access to the newly created values generated
- `||`: Any outer `&&` does not propagate the bound values within either side of the `||`, expect when each sub-condition has a bound value with the same name and type as in any other sub-condition.

### Optional shorthand [↵](#branch-conditions--let-bindings-)
```
<opt-shorthand> := [ <name> '=' ] <expr> '?'
```

Within a condition, any use of the `?` operator on an optional in a top-level expression is syntactic sugar for `let Some(name) = name`, implicitly upgrading the optional value to a non-optional value that can be used.
If the expression is not a name, it can be bound by assigning it to a variable.

### Let bindings [↵](#branch-conditions--let-bindings-)
```
<let-binding> := 'let' <pattern> '=' ? <scrutinee> excluding lazy boolean operator expressions ?
```

Within a condition, a special `let` binding can appear, this allows for pattern matching inside of the condition.
The `let` binding evaluates to a true value when the scrutinee matches the provided pattern.

If a `let` binding binds any values, these values may then be used anywhere in the conditional chain that is following it, when it is separated by `&&`.

Multiple pattern may be specified using the `|` operator, and has similar semantics as `|` in  [`match` expression](./match-expressions.md).
This includes that to use any bound values later on in the chain, it needs to be present in all sub-patterns, with the same bind mode and type.

## Result if expressions [↵](#if-expression)
```
<res-if-expr> := 'if' [ <name> '=' ] <expr> '!' <block> 'else' '(' <name> ')' <block>
```

A result if expression is a special variant of an if expression that allows you to handle result types without requiring a `match` expression.
It is syntactic sugar, allowing a value to be checked, and handled accordingly.
If the result is a valid value, the name of the result value can either be directly used, or if the expression is not a name, be bound via an assignment.
The else case is then also provided with a named variable representing the error within the result, this will be moved into the bound variable name.

> _Note_: This is equivalent to
> ```
> match <expr> {
>     Ok(value) => { ... },
>     Err(err)  => { ... },
> }
> ```



[`match` expression]: ./match-expressions.md