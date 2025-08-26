# Loop expressions
```
<loop> := <loop-expr>
        | <while-expr>
        | <res-while-expr>
        | <do-while-expr>
        | <for-expr>
        | <labelled-block-expr>
```

there is support for five loop expressions:
- a `loop` expression denoting an infinite loop
- a `while` expression looping until a predicate is false
- a `do while` expression looping until a predicate is false, guaranteeing to run the loop at least once.
- a `for` expression extracting a value from an iterator, looping until the iterator is empty
- a labelled block expression running a loop exactly once, but allowing the loop to exit early with `break`

All five types of loop expression support `break` expressions and labels.
All except labelled block expressions support `continue` expressions.

The condition of a loop, or the source of a for loop may not contain a struct expression without being wrapped in a block or parentheses.

In the following locations in loops may not contain a struct expression, without being wrapped in a block or parentheses, or in a let binding.
- a `while` condition
- a `for` source

> _Note_: In the context of loops, a non-trivial value means any value that is either of type `()` or `!`

## Loop expression [↵](#loop-expressions)

> _Todo_: Fix up if never type is changed

```
<loop-expr> := <label> 'loop' <block>
```

A `loop` expression repeats execution of a body continuously.

A `loop` expression without an associated `break` expression is diverging and has type `!`.
A loop expression containing an associated `break` expressions can terminate, and must be type compatible with the value of the other `break` expressions.

## While expression [↵](#loop-expressions)

```
<while-expr> := <label> 'while' <branch-condition> [ ';' <expr> ] <basic-block> [ 'else' <block> ]
```

A `while` loop begins by evaluating the loop condition operand.
If the loop conditional operand evaluates to `true`, the loop block executes, the control return to the loop conditional operand.
If the loop conditional expression evaluates to `false`, the `while` expression completes.

A while loop can also have an increment expression, this is followed after the branch condition, and separated by a ';'.
This expression will be called at the end of each loop.
The increment can be used emulated C-style for-loops.

### While else [↵](#while-expression-)

`while` loops have support for an else statement, these can be used for 2 distinct reasons:
- if a `while` contains a `break` with a non-trivial value, an else statement is required to be added, and will be executed if no `break` is hit.
  It must therefore return a value with the same type as the type of the value provided to the `break`.
- if a `while` contains no `break` or one that only returns a trivial value, the else statement will only be executed if the first iteration is not entered.
  Meaning that this only will happen when the initial condition returns `false`, not when the loop break on a subsequent iteration.

### Result while [↵](#while-expression-)
```
<res-while-expr> := <label> 'while' [ <name> '=' ] <expr> '!' <block> 'else' '(' <name> ')' <block>
```

Similar to a result `if`, a result `while` is syntactic sugar for handling result types within a while loop, without needing to have a nested `match`.
If the result is a valid value, the while loop is entered and the value can either be directly used, or if the expression is not a name, be bound via an assignment.
If any iteration of the loop result in an error, the else block will be executed, and the error will be moved into the bound variable name.


> A result while is equivalent to
> ```
> :result_while: loop {
>     match <expr> {
>         Ok(value) => { ... }
>         Err(err) => { ...; break :result_while:; }
>     }
> }
> ```

## Do-while expression [↵](#loop-expressions)

```
<do-while-expr> := <label> 'do' <block> 'while' <expr> [ 'else' <block> ]
```

A `do while` loops begins by running the body of the loop at least once, after which the boolean loop condition operand is evaluated.
If the loop conditional operand evaluates to `true`, the loop block executes, the control return to the loop conditional operand.
If the loop conditional expression evaluates to `false`, the `do while` expression completes.

### Do while else [↵](#do-while-expression-)

`do while` loops have support for an else block whenever the loop contains a `break` with a non-trivial value.
Then an else statement is required to be added, and will be executed if no `break` is hit.
It must therefore return a value with the same type as the type of the value provided to the `break`.

## For expression [↵](#loop-expressions)

```
<for-expr> := [ <label> ] 'for' <pattern> 'in' <expr> <block> [ 'else' <block> ]
```

A `for` expression is a syntactic construct for looping over elements provided by an implementation of `IntoIterator`.
If the iterator yields a value, that value is matched against the irrefutable pattern, the body of the loop is executed, and then control returns to the head of the `for` loop.
If the iterator is empty, the `for` expression completes.

### For else [↵](#for-expression-)

`for` loops have support for an else statement, these can be used for 2 distinct reasons:
- if a `for` contains a `break` with a non-trivial value, an else statement is required to be added, and will be executed if no `break` is hit.
  It must therefore return a value with the same type as the type of the value provided to the `break`.
- if a `for` contains no `break` or one that only returns a trivial value, the else statement will only be executed if the first iteration is not entered.
  Meaning that this only will happen when the initial condition returns `false`, not when the loop break on a subsequent iteration.

### Result for [↵](#for-expression-)
```
<res-for-expr> := [ <label> ] 'for' <pattern> '!' 'in <expr> <block> 'else' '(' <name> ')' <block>
```

Similar to a result `if`, the result `for` is syntactic sugar for handlign resutl types whitin a for loop, without needing to have a nested `match`.
If the result of an iteration is a valid value, the for loop is entered and the values bound in the pattern can either be directly used.
If any iteration of the loop result in an error, the else block will be executed, and the error will be moved into the bound variable name.

## Labelled block expressions [↵](#loop-expressions)

```
<labelled-block-expr> := <label> <block-expr>
```

Labelled block expressions are exactly like block expressions, except they allow using `break` expressions within the block.
Unlike loops, `break` expressions within a labelled block experssion must have a label (i.e. the label is not optional).
Similarly, labelled block expressions must begin with a label.

## Loop labels [↵](#loop-expressions)

```
<label> := ':' <name> ':'
```

A loop expression may optionally have a label.
If the label is present, the labeled `break` and `continue` expressions nested within the loop may exit out of this loop or return control to its head.

Labels follow the hygeine and shadowing rules of local variables.
