# Special operators

Some operators have some specific syntax to them that cannot be directly implemented and need compiler support.
These are called 'special operators'.

### Borrow operators [↵](#special-operators)
```
<borrow-op> := '&' [ 'raw' ] [ 'mut' ]
```

Borrow operators are prefix operators.
When applied to a place expression, the operator takes a reference (of pointer) to the location the value refers to.
The memory location is then put in a borrowed state for the duration of the reference, meaning until the last use of the borrowed value.

If a shared borrow (`&`) is taken, the value within the location cannot be mutated, but can be shared and read.
Otherwise when a mutable borrow (`&mut`) is taken, the value within the location can be mutated and read, but cannot be shared.

When the operator is applied to a value expression, a temporary value will be created.

The associated for traits for these operators are
- `&`: `Borrow`
- `&mut`: `BorrowMut`

This operator has the `BorrowDeref` precedence.

#### Raw borrow operators [↵](#special-operators)

The raw borrow operators are special cases of the borrow operator, which instead of borrowing the values, get the memory address of the value.
This is indicated using the `raw` keyword.

`&raw` creates a `^T`, and `&raw mut` creates a `^mut T`.

The raw borrow operators should be used in a place where the borrow operator could evaluate to a place that is not properly aligned, or does not store a valid value as determined by its type, or whenever creating a reference that would introduce inccorrect aliassing assumptions.
These are situations which would result in [illegal behavior](../illegal-behavior.md).

Unlike other operators, the raw borrow operator cannot be implemented.

### Derefence operator [↵](#special-operators)
```
<deref-op> := '^'
```

The derefence operator is a postfix operator.
When applied to a pointer, in denotes a pointed-to locaton.
If the expresson is of type `&mut T` or `^mut T`, and is either a local variable or (possibly nested) field variable, or is a mutable-place operator, then the resulting memory location can be assigned to.

Dereferencing a raw pointer requires an `unsafe` context.

A special variant of the deref operator is `DerefMove`, allowing either the derefernced value or one of its fields to be moved out of its location.
A useful example of this would be for types such as smart-pointers.

The exact variant of the derefernce will be decided based on the surrounding context

The associated traits for this operator are:
- immutable: `Deref`
- mutable: `DerefMut`
- move: `DerefMove`

This operator has the `BorrowDeref` precedence.

### Try operator [↵](#special-operators)
```
<try-op> := '?' | '!'
```

Try operators are postfix operators.

Try operator are used to affect the control flow of a function when an erronous value is produced.
If a valid value will be generated, it will return this value.

#### Propagating try [↵](#1423-try-operator-)

The propagating try operator (`?`) allows a function to shortcut an return an erronous value from the current function.
It will cause also all in-scope [defer-on-error statements](../statements/defer-statements.md#defer-on-error-statement-) to be evaluated.

The associated trait for the operator is `Try`

> _Note_: This should not be confused with the ['err'-checked field access](../expressions/field-access-expressions.md)

The operator has the `Unary` precedence.

#### Unwrapping try [↵](#1423-try-operator-)

The unwrapping try operator (`!`) will cause a program to panic if an erronous value is encountered.

The associated trait for the operator is `TryUnwrap`

The operator has the `Unary` precedence.

## Contract capture operator [↵](#special-operators)
```
<contract-capture-op> := '$'
```

Contract capture operators are postfix expressions.

Contract operators are only allowed inside of `post` contracts.
They allow the value of an expression to be captures at the start of a function, to use it in the post contract at the end of the function.
The value captures must be a a `Copy` type.

This operator cannot be manually implemented, as `$` has multiple special meanings.

The operator has the `Unary` precedence.

## Logical boolean operators [↵](#special-operators)
```
<lazy-bool-op> := '&&' | '||'
```

Lazy boolean operators are infix operators.
Lazy boolean operators can only be applied to boolean values and cannot be overloaded.
`||` represents a logical or, and `&&` represents a logical and, they differ from tier single character counterparts `|` and `&`, in that the right hand operand is only evaluated if the left-hand operand does not already determine the result of the expression.

That is, `||` only evaluates the right-hand operand if the left-hand operand evaluates to `false`.
On the other hand, the `&&` only evaluated the right-hand operand if the left-hand operand evaluates to `true`.

They are [lazy infix operators](../operators.md#lazy-)

The `&&` operator has the `LogicalAnd` precedence, and `||` has the `LogicalOr` precedence.

## catch operator [↵](#special-operators)
```
<catch-op> := '??'
```

Catch operators are infix operators.

This is similar to the or-else operator, but instead of being based on a 'thruthy' value, it is based on an explicit erronous value.

This is shorthand for the non-error capturing [catch expression](../expressions/catch-expressions.md).
Unlike the catch expression, this operator follows a different precedence.

The associated trait is `Catch`.

The operator has the `Select` precedence.

## In-place operator [↵](#special-operators)
```
<inplace-operator> := '<-'
```

The in-place operator allows for the direct construction of a type directly in its memory location.
This can be useful in case the type takes up a lot of space and allows to avoid generating an intermediate.

The in-place operator may take either of the following expressions on it's right-hand side:
- a [constructing expression](../expressions/constructing-expressions.md)
- a function or closure, taking a single argument of type `&mut MaybeUninit(T)`
- an [initializer](../items/initializers.md)

This operator has no associated trait (i.e. can't be manually implemented), and has assign precedence.
