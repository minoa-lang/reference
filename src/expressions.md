# Expressions
```
<expr>            := <expr-with-block> | <expr-no-block>
<expr-with-block> := <block-expr>
                   | <if-expr>
                   | <loop>
                   | <match-expr>
<expr-no-block>   := <literal-expr>
                   | <path-expr>
                   | <unit-expr>
                   | <underscore-expr>
                   | <operator-expr>
                   | <paren-expr>
                   | <type-cast-expr>
                   | <type-check-expr>
                   | <construct-expr>
                   | <index-expr>
                   | <tuple-index-expr>
                   | <call-expr>
                   | <method-expr>
                   | <op-method-expr>
                   | <field-expr>
                   | <closure-expr>
                   | <closure-var-expr>
                   | <full-range-expr>
                   | <break-expr>
                   | <continue-expr>
                   | <fallthrough-expr>
                   | <return-expr>
                   | <throw-expr>
                   | <comma-expr>
                   | <template-string-expr>
                   | <key-path-expr>
                   | <await-expr>
                   | <yield-expr>
                   | <meta-expr>
                   | <when-expr>
```

An expression is code which has one or both of the following roles:
- to produce a value to be used, or
- to apply any side-effects that occur during its execution

Each expression can contain multiple sub-expressions, these are called the operands of the expression.
An expression dictates the following in regards to its operands:
- whether or not to evaluate the operands when evaluating the expression
- the order in which to evaluate the operands
- how to combine the operands' values to obtain the result of the expression

In this way, the structure of the expression dictates the structure of execution.

## Expression precedence & operator evaluation [↵](#expressions)

Expressions have a fixed set of precedences, of which some expression may determine the precedence within their category.
These are laid out in the following table, in order of highest to lowest precedence

expression                                                                 | has additional precedence
---------------------------------------------------------------------------|---------------------------------
parenthesizes expression                                                   | no
literals, underscore, unit                                                 | no
paths, tuple indexing, template string, key-path, full range, closure vars | no
method calls                                                               | no
field accesses                                                             | no
function calls, indexing, constructing                                     | no
type casts, type checks                                                    | no
`try`, `catch`, `await`                                                    | no
comma expression operands of operators                                     | yes, see [chaining operators]
operators                                                                  | yes, see [operator precedences]
comma expressions                                                          | no
assignment expressions                                                     | no
`return`, `break`, `continue`, `fallthrough`, `throw`, `yield`, closures   | no

In general, all operands of the expression will be evaluated before any side-effects will be applied, and they are evaluated from left-to-right.
If an expression deviates from this, it will defined if and in which order their expressions are evaluated.

## Place, value, & assign expressions [↵](#expressions)

Expressions are split up in 3 different categories
- place expressions
- assignee expressions
- value expressions

Whithin each expression, operands may likewise occur in eitehr place or values contexts.
The evaluation of an expression depends both on its category and the context in which it occurs.

> _Note_: For the purpose of an expression's kind, any parentheses are essentially ignored, as they have the same kind as their inner expressions

### Place expressions [↵](#place-value--assign-expressions-)

A place expressions refers to any expression which evaluates to a location within memory.

These expressions are produced by one of the following:
- paths to
  - [local variables]
  - [static variables]
- [dereferences] (`*expr`)
- [operator expressions]
- array indexing or [index expressions] which return place expressions (`expr[expr]`)
- [field] references (`expr.field`)
- [function] or [method] calls returning place expressions
- [properties] returning place expressions

The following locations are place expression contexts, i.e. location where place expressions are expected
- the left operand of:
  - [assign operators]
  - [in-place operator]
- the unary operand of:
  - [borrow operators]
  - [raw borrow operators]
  - [dereference operators]
- the operands to a [field access]
- the indexed operand of array indexing
- operand of any [implicit borrows]
- initializer of a [pattern variable declaration]
- scrutinee of [match] and [let bindings]

### Assignee expressions [↵](#place-value--assign-expressions-)

An assignee expressions is a expressions which can be used in locations where an assignment occurs.

These consist of the following:
- place expressions
- underscore
- tuples of assignee expressions
- slices of assignee expressions
- tuple structs of assignee expressions
- struct of assignee expressions (with optionally named fields)
- unit structs
- bitfields of assignee expressions (with optionally named fields)

### Value expressions [↵](#place-value--assign-expressions-)

A value epression is any expressioo which is not a place expressions.
These expressions represents an actual value, which is not located in memory.

## Moves & copied types [↵](#expressions)

When a place expressions is evaluated as a value expression, or is bound to a value expression in a pattern, it denotes the value held in that memory location.

If this type implements `Copy`, then the value will be copied, unless it is an argument for an explicit [`move` parameter].

Othewise, if a value is `Sized`, then the value will possible be moved.
Specifically, the following place expressions may be moved out of:
- [local variables] that are currently not borrowed
- temporary values
- fields of a place expression which can be moved out of and don't implement `Drop`
- the result of a [dereference] which implements `DerefMove`

After moving out of a place expression which evaluates to a [local variables], the location is deinitialized and marked as invalid, meaning it cannot be read from until it is once again is re-initialized.

In all other cases, a place expression in the location of a value expression will result in an error.

## Mutability [↵](#expressions)

For a place expression to be able to be assigned to, it needs to be mutable, either by being mutably referenced (either explicit or implicitly), or must be explicitly refered to as mutable in a pattern or variable declaration.
These are called _mutable place expression_, all other place exprssions are _immutable place expressions_.

The following locations are mutable place expression context:
- mutable variables which are currently not borrowed
- mutable [static variables]
- temporary variables
- fields: this evaluates the sub-expression in a mutable place context
- dereferences of mutable pointers, i.e. `^mut T`
- dereferences of a variable, or field of a variable, with type `&mut T`
- dereferences of a type that supports mutable derefernces, i.e. when the type implements `DerefMut`. This then requires that the value bieng dereferences is evaluated in a mutable expression context
- indexing of a type that implements `IndexMut`, this then evaluates the indexed value (not the index) in a mutable place context

## Temporaries [↵](#expressions)

When using a value expression in a location a place expression is expected, a temporary unnamed memory location is created (usually on the stack) and is set to the value of the expression creating and initializing the temporary value.
This expression than evaluarates to that location instead, except if [promoted] to a `static`.
If not promoted, they will be dropped at the end of the expression's scope.

## Implicit borrows [↵](#expressions)

Certain expression will treat an expression as a palce expression by implicitly borrow it.

Values will be implicitly borrowed in the following expression:
- left operand in a [method] call
- left operand in a [field access]
- left operand in a [function] call
- left operand in a [index expression]
- operands to an operator, implemented for type `T`, but taking a parameter as `&T`


[implicit borrows]:             #implicit-borrows-
[function]:                     ./expressions/call-expressions.md
[field]:                        ./expressions/field-access-expressions.md
[field access]:                 ./expressions/field-access-expressions.md
[let bindings]:                 ./expressions/if-expressions.md#let-bindings-
[index expression]:             ./expressions/index-expressions.md
[index expressions]:            ./expressions/index-expressions.md
[match]:                        ./expressions/match-expressions.md
[method]:                       ./expressions/method-expressions.md
[operator expressions]:         ./expressions/operator-expressions.md
[`move` parameter]:             ./items/functions.md#parameter-specifiers-
[static variables]:             ./items/statics.md
[properties]:                   ./items/properties.md
[assign operators]:             ./operators.md#assginment-operators-
[chaining operators]:           ./operators.md#chain-
[borrow operators]:             ./operators/special-operators.md#borrow-operators-
[dereference]:                  ./operators/special-operators.md#derefence-operator-
[dereferences]:                 ./operators/special-operators.md#derefence-operator-
[dereference operators]:        ./operators/special-operators.md#derefence-operator-
[in-place operator]:            ./operators/special-operators.md#in-place-operator-
[raw borrow operators]:         ./operators/special-operators.md#raw-borrow-operators-
[operator precedences]:         ./precedences.md
[local variables]:              ./statements/variable-declarations.md
[pattern variable declaration]: ./statements/variable-declarations.md#pattern-variable-declaration-
[promoted]:                     ./type-system/destructors.md#constant-promotion-