# Parenthesized expressions
```
<paren-expr> := '(' <expr> ')'
```

A parenthesized expression, also known as a grouped expression, wraps a single expression, allowing the expression to be evaluated before any other expressions that are outside of the parentheses will be executed.
The expression will have the same type and value as the sub-expression within the parentheses.

Parentheses explicitly increase the precedence of this expression above that of other expressions, allowing expressions that would have a lower precedence to be executed before outer expressions use this expression.

> _Example_
> ```
> x := 2 + 3 * 4; // not parenthesized, so the multiplication happens before the addition, resulting in 14
> assert(x == 14);
> 
> x := (2 + 3) * 4; // parentehsized, so the addition happens before the multiplication, resulting in 20
> assert(x == 20);
> ```

> _Example_
> 
> Another usecase where this become useful would be on fields with a function pointer type
> 
> ```
> a.f(); // Call to the method `f`
> (a.f)(); // call to the function stored in field `f`
> ```