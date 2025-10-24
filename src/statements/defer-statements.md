# Defer statements
```
<defer-stmt> := 'defer' <defer-body>
<defer-body> := <expr-with-block>
              | <expr-no-block> ';'
```

A defer statement delays the execution of a given piece of code until the end of the scope it is declared in.

> _Example_
> ```
> mut a := 2;
> 
> {
>     defer a = 3;
> 
>     // Prints "2"
>     println(a);
> 
> } // Defer is run at the end of this block
> 
> // Prints "3"
> println(a);
> ```

A defer is executed before variables are being dropped, and are executed in the reverse order they were declared in the current scope.

> _Example_
> 
> Runninng the following piece of code
> ```
> {
>     a := PrintOnDrop("Variable a");
> 
>     defer println("Defer 0");
>     defer println("Defer 1");
> 
>     a := PrintOnDrop("Variable b");
> }
> ```
> will result in the following output
> ```
> Defer 1
> Defer 0
> Variable b
> Varaible a
> ```

When a defer takes a reference, either shared or mutable, to a value, the value must be available mutably after any return expression.
This also extends the lifetime of whatever value is pointed to until the end of the function.

> _Example_
> ```
> fn(&self) foo() -> &i32 {
>     // We can still use a reference to `self.val` after this, as we only need it mutable after the function exists
>     defer foo_mut(&mut self.val);
>     defer bar(&self.val);
> 
>     // error: `self.val` is already mutably referenced by a defer
>     // error| `self.val` cannot have a mutable reference taken, as one is already taken by a refer
>     // return &mut self.val;
> 
>     // error: `self.val`cannot have a reference taken, as a future mutable reference is already taken by a refer
>     // return &mut self.val
> 
>     // error: cannot move our of `self.val`, as a future mutable reference is already taken by a refer
>     // a := self.val;
> }
> ```

A defer may not contain any expression which would exit a function, meaning one of:
- [`return` expression]
- [`yield` expression]
- [`throw` expression]

As the defer is run after these expressions are executed, meaning it also cannot be used to assign a named return.

## Error defer
```
<err-defer-statmt>  := 'errdefer` [ <err-defer-capture> ] <defer-body>
<err-defer-capture> := '(' <name> ')'
```

An error defer is a special variant of a defer statement, which is only run when an error is returned using one of the following:
- a [`throw` expression]
- a use of the [catch operator]

The defer will only execute when the errr is retured in the same scope (or any nested scopes) as the error defer.
Like other defers, all errdefers within the current scope will be executed in reverse order.

> _Example_
> ```
> fn foo() -> Error!i32 {
>     errdefer println("0");
>     errdefer println("1");
> 
>     // Depending on the result, if an error is returned, it will return that error and call all `errdefer`'s.
>     ret_error()?;
> 
>     // Returns `.Err(Error)`, and calls `errdefer`'s
>     throw Error;
> }
> ```
> No matter which expression causes the `errdefer`'s to be exectued, the output will be
> ```
> 1
> 0
> ```

> _Note_: An error defer can be avoided by explicitly returning an erronous value, e.g. `return .Err(err)`.
> 
> > _Example_
> > ```
> > fn foo() -> Error!i32 {
> >     errdefer println("defer");
> > 
> >     // Does not exectue the above defer
> >     return .Err(Error);
> > }
> > ```

An error defer is also allowed to capture an error.

> _Example_
> ```
> errdefer(err) println("err: \{err}");
>
> throw Error;
> ```
> outputs
> ```
> err: Error
> ```



[`return` expression]: ../expressions/return-expressions.md
[`yield` expression]:  ../expressions/yield-expressions.md
[`throw` expression]:  ../expressions/throw-expressions.md
[catch operator]:      ../operators/special-operators.md#catch-operator-