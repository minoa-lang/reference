# Variable declarations
```
<var-decl> := <name-var-decl> | <let-var-decl>
<name-var-decl> := <var-decl-name> { ',' <var-decl-namef> }* ':=' <expr> ';'
<var-decl-name> := [ 'mut' ] <name>
<let-var-decl> := 'let' <pattern-top-no-alt> [ ':' <type> ] [ '=' <expr> [ 'else' <block-expr> ] ] ';'
```

A variable declaration introduces (a) new variable(s) withing a scope.
By default, variables are immutable and need to explicitly be defined as `mut` to be able to be mutated.

If no type is provided, the compiler will infer the type from the surrounding context, or will return an error if insuffient information can be retreived from code.

Any variable introduced will be visible until the end of the scope, unless they are shadowed by another declaration.

When using a 'name' variable declaration with more than 1 name, the expression must be one of the following:
- An comma separated list of expression (i.e. a comma expression)
- A tuple expression
- An expression returning a tuple
If either of the latter 2 is provided, the declaration will automatically destructure the tuple into the variables.
The number of elements within any of the expressions need to match the number of names provided, otherwise a compiler error will be generated.

When using a `let` variable declaration, a variable may also be declared as being unitialized, this requires:
- An explicit type to be given
- Only identifiers or tuple patterns are provided
- The variable needs to be assigned a value in all possible paths that can reach the first use of that variable.

A `let` variable declaration may also contain an `else` block, allowing a refutable pattern.
If this patten fails to match, the else block will get executed, this is generally used to either panic or return from the function.
If an `else` block is not present, the pattern needs to be irrefutable.
