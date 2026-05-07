## Unsafe Expression
```
<unsafe-expr>    := 'unsafe' <unsafe-sub-expr>
<unsafe-sub-exp> := <block-expr>
                  | <paren-expr>
                  | <literal-expr>
                  | <path-expr>
                  | <unit-expr>
                  | <underscore-expr>
                  | <unsafe-expr>
                  | <prefix-op-expr>
                  | <postfix-op-expr>
                  | <construct-expr>
                  | <index-expr>
                  | <tuple-index-expr>
                  | <call-expr>
                  | <method-expr>
                  | <field-access-expr>
                  | <closure-expr>
                  | <closure-var-expr>
                  | <full-range-expr>
                  | <template-string-expr>
                  | <meta-expr>
                  | <when-expr>
```

An `unsafe` expression is an expression which allows for any `unsafe` operation to evaluated.

The expression allows a limited set of expression to appear directly after it, any other expression must be either within an expression block or a parenthesized expression.
If wrapped in either of these, the entire content of the expression will be run in an unsafe mode.

> _Note_: It is recommended to keep the expression to which the `unsafe` expression is applied on, to have an, as narrow as possible, scope

> _Example_
> ```
> union U {
>     i: i32
> }
> 
> u := U{ i: 1 };
> val := unsafe u.1;
> 
> ptr := get_pointer();
> val := unsafe ^ptr;
> ```