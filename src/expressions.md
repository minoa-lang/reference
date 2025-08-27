# Expressions
```
<expr> := <expr-with-block> | <expr-no-block>
<expr-with-block> := <block-expr>
                   | <if-expr>
                   | <loop>
                   | <match-expr>
<expr-no-block> := <literal-expr>
                 | <path-expr>
                 | <unit-expr>
                 | <operator-expr>
                 | <paren-expr>
                 | <in-place-expr>
                 | <type-cast-expr>
                 | <type-check-expr>
                 | <contructing-expr>
                 | <index-expr>
                 | <tuple-index-expr>
                 | <call-expr>
                 | <method-call-expr>
                 | <field-access-expr>
                 | <closure-expr>
                 | <closure-arg-expr>
                 | <op-method-expr>
                 | <trailing-closure-expr>
                 | <full-range-expr>
                 | <break-expr>
                 | <continue-expr>
                 | <fallthrough-expr>
                 | <return-expr>
                 | <underscore-expr>
                 | <throw-expr>
                 | <comma-expr>
                 | <when-expr>
                 | <template-string-expr>
                 | <key-path-expr>
                 | <await-expr>
                 | <yield-expr>
                 | <meta-expr>
```

Expressions do 2 things:
- create a value
- produce a side-effect

Each expression will return the value produced by it, while also applying any effect during evaluation.
An expression can contain multiple sub-expressions, which are called the operands of an expression.

Each expression dictates the following:
- Whether or not to evaluate the operands when evaluating the expression.
- The order in which to evaluate the operands
- How to combine the operands' values to obtain the value of the expression.

In this way, the structure of the expression dictates the structure of execution.

The precedence of expressions follow the order defined below

expressions                                                     | associativity
----------------------------------------------------------------|---------------
paths                                                           |
method calls                                                    |
field accesses                                                  | left-to-right
functions calls, indexing                                       | left-to-right
`try`, `catch`                                                  |
comma expression arguments to operators                         | left-to-right
operators (see the [precedence section])                        |
other comma expression                                          | left-to-right
`return`, `break`, `continue`, `fallthrough`, `throw`, closures |

In general, the operands of an expression will be evaluated before any side-effects will be applied, and the operands are evaluated from left to right.
Each expression that deviates from this order, will define if and in which order their expressions are evaluated.

## Expression details [↵](#9-expressions-)

### Place, value & assign expressions [↵](#91-expression-details-)

Expressions can be divided in 3 categories:
- Place expressions.
- value expressions.
- Assign expressions.

With each expression, operands may likewise occur in either place or value context.
The evaluation of an expression depends both on its own category and the context it occurs in.

#### Place expressions [↵](#place-value--assign-expressions-)

A place expression represents an expression that points to a location in memory.

They refer to the following expressions:
- Local variable, like a path
- Static variables, like a path
- Dereferenced addresses or references
- Indexing resulting in a place expression
- Field references
- Parenthesized place expressions
- Any call (function and operator) that results in a place expression
- Any property resulting in a place expression

#### Assign expressions [↵](#place-value--assign-expressions-)

An assign expression is any expression which can appear on the left hand side of an assignment operator.

They refer to the following expressions:
- Place expressions
- Underscores
- Tuples of assign expressions
- slices of assign expressions
- Tuple structs of assign expressions
- Aggregate structs of assign expressions (with possible named fields).
- Unit structs

#### Value expressions [↵](#place-value--assign-expressions-)

Any expression that does not fit [place] or [assign] expressions are value expressions

### Moved & copied types [↵](#expression-details-)

When a place expression is evaluated as a value expression, or is bound to a value expression in a pattern, it denotes the value held in that memory location.
If the type is copyable, then the value will be copied, otherwise if the value is sized, it is moved.
Only the following place expressions can be moved out from:
- variables that are not currently borrowed
- temporary fields
- fields of place expressions that can be moved out of, if the field does not need to be dropped or used in a drop implementation, i.e. if the field can be partially moved
- Result of a expressions that supports moving out of, i.e. a type that implements `DerefMove`.

After moving out of a place expression that is evaluated in a local expression, the location is then deinitialized and cannot be read from again until it is reinitialized.

In all other places, a place expression in a value expression will result in an error.

### Mutability [↵](#expression-details-)

For a place expression to be able to be assigned to, it needs to be mutable, either by being mutably referenced (either explicitly or implicitly), or must be explicitly refered to as mutable in a pattern.
These are called _mutable place expression_, all other place expressions are _immutable place expressions_

The following expressions can be used as mutable expressions:
- Mutable variable that is currently not borrowed.
- Temporary values
- Fields
- Dereferences of mutable pointers, i.e. `^mut T`
- Dereferences of a variable or a field of one, with a type of `&mut T`
- Dereferences of types supporting mutable dereferences, i.e. when the type implements `DerefMut`
- Any expressions that results in a place expression that is mutable

### Temporaries [↵](#expression-details-)

When using a value expression in a location a place expression is expected, a temporary unnamed memory location is created (usually on the stack) and is set to the value of the expression creating the temporary.
The temporary value will then be used as the place expressions and will be dropped at the end of the expression's scope.

### Implicit borrows [↵](#expression-details-)

Certain expressions will treat an expression as a place expression by implicitly borrowing it.

Implicit borrowing takes place in the following expressions:
- Left operand in a method call
- Left operand in a field expression
- Left operand in a call expression
- Left operand in an index expression
- Operand of a derefence operator
- Operands of a comparison
- Left operand of a compound assignment



[assign]:             #assign-expressions-
[place]:              #place-expressions-
[precedence section]: ./precedences.md