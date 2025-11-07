# list comprehension expression
```
<list-comprehension>      := <simple-list-comprehension>
                           | <loop-list-comprehension>
```

A list comprehension is a special expression allows an expression to generate a set of values.
It can be seen as a special version of a [generator closure expression].

List comprehensions are limited to a subset of locations:
- in an [array comprehension expression]
- in a [vectorized comprehension expression]
- as an argument to a parameters implementing the list-comprehenion traits defined below

All list comprehension expressions have an anonymous type which will be in either of the following forms:
- variants returing individual values
  - `ListComprehension() -> T`
  - `ListComprehension(&self) -> T`
- variants returning individual values and an optional location to insert the value
  - `ListComprehensionWithLoc() -> (?Loc, T)`
  - `ListComprehensionWithLoc() -> (?Loc, T)`

Whether the element produces by a list comprehension is allowed to provide a location to place the value depends on location it is defined.
This means it's allowed for both array and vectorized comprehension expressions, and parameters implementing `ListComprehensionWithLoc`.

> _Note_: Below examples are shown with the comprehension located in an [array comprehension expression].

### Simple list comprehension [↵](#list-comprehension-expression)
```
<simple-list-comprehension> := <list-comprehension-elem> 'for' <pattern> 'in' <expr> [ 'if' <expr> ]
<list-comprehension-elem> := [ <expr> ':' ] <expr>
```

The first form allows for a simple expression to be provided, consisting of:
- an array elements defining the resulting value
- a pattern to match an iteration to
- an iterable expression
- an optional condition

While iterating over the provided iterable expression, it is possible to skip a value if:
- the pattern does not match
- the `if` condition evaluates to `false`

> _Example_
> ```
> // simple list comprehension
> a := [num for num in 0..=3];
> // will produce
> a := [0, 1, 2, 3];
> 
> // list comprehension with an expression
> b := [num * 2 for num in 0..=3];
> // will produce
> b := [0, 2, 4, 6];
> 
> // list comprehension with a condition
> c := [num for num in 0..=3 if num & 1 == 0];
> // will produce
> c := [0, 2];
> ```

A special case happens where if the condition applies the expression `expr?` on a value of type `?T`, and the value is used within the array element, instead of propagating this error to the surrounding funtion in case of a `null` value, it will filter out this value.
This is equivalent to an [optional shorthand] in a regular `if` expression.

> _Example_
> ```
> // in a case where `opt_iter` causes `val` to be of type `T`.
> a := [val for val in opt_iter if val?];
> 
> // this would be equivalent to
> a := [for val in opt_iter {
>     if val? {
>         yield val;
>     }
> }];
> ```

Similarly to a list expression, the array element may be explicitly provided with a location into the array a value should be inserted.

> _Example_
> ```
> a := [3 - i: i for i in 0..=3];
> // will result in
> a := [3, 2, 1, 0];
> ```

### Loop list comprehension [↵](#list-comprehension-expression)
```
<loop-list-comprehension> := <loop-expr>
```

For more complex list comprehensions, the expression may be written as a loop.

The loop expression will be interpreted as if it were in a special function boundary, therefore the following expression will act as such:
- [`return`]
- [`yield`]
- [`break`], [`continue`] or [`fallthrough`], these are only allowed to reference labels inside of the list comprehension

However, both [`throw`] expression and the [catch operator] will not act as such, and will propagate any error to the containing function.

> _Note_: To produce a list comprehension with a location, the list comprehension element variants of the respective returning expression should be used.

> _Example_
> ```
> a := [for i in 0..=10 {
>     // add even numbers to the array
>     if i & 1 {
>         yield i;
>     }
> 
>     // early out of the iteration
>     if i == 7 {
>         return i;
>     }
> }];
> // will produce
> a := [0, 2, 4, 6];
> 
> // list comprehension using the implicit `self` value
> b := [while self.len() < 4 {
>     yield self.last() + 2;
> }];
> // will produce
> b := [0, 2, 4, 6];
> ```

> _Implementation note_: Loop list comprehensions are parsed as loop expression and are only resolved later on in the compilation process


[`break`]:                             ./break-expressions.md
[generator closure expression]:        ./closure-expressions.md#genetors "Section does not exists yet"
[array comprehension expression]:      ./constructing-expressions/array-expressions.md#array-comprehension-expression-
[vectorized comprehension expression]: ./constructing-expressions/vectorized-expressions.md#vectorized-comprehension-expression-
[`continue`]:                          ./continue-expressions.md
[`fallthrough`]:                       ./fallthrough-expressions.md
[optional shorthand]:                  ./if-expressions.md#optional-shorthand-
[`return`]:                            ./return-expressions.md
[`throw`]:                             ./throw-expressions.md
[`yield`]:                             ./yield-expressions.md
[catch operator]:                      ../operators/special-operators.md#catch-operator-