# Destructors

When an initialized variable or temporary goes out of scope, its destructor is run, or it is _dropped_ (this terminology is taken from rust).
Assignment also runs the destructor of its left-hand operatnd, if it's initialized.
If a variable has been partially initialized, only its initialized fields are dropped.

The destructor of a type `T` consists out of:
1. If `T is Drop`, calling `<T as Drop>::drop`, or
2. Recursively running the destructor of all its fields
    - The fields of a struct or record are dropped in declaration order
    - The fields of the active enum or enum record variant are dropped in declaration order
    - The fields of a tuple are dropped in order
    - The elements of an array or owned slice are dropped from the first element to the last.
    - The variables that a closure captures by move are dropped in an unspecified order
    - Trait objects run the destructor of the underlying type
    - Other types don't result in any further drops

If a destructor must be run manually, such as when implementing a smart pointer, `drop_in_place` can be used.

## Drop scopes [↵](#destructors)

Each variable or temporary is associated with a drop scope.
When control flow leaves a drop scope, all variables associated to that scope are dropped in reverse order of declaration (for varialbes) or creation (for temporaries).
Values are only dropped after running all `defer` statements within the same scope.

Drop scopes are determined after replacing `for`, `if` and `while` expressions (with let bindings) with the equivalent using `match`.
Overloaded or user-defined operators are not distinguished from built-in operators and binding modes are not considered.

Given a function, or closure, there aer drop scopes for:
- The entire function
- Each statement
- Each expression
- Each block, including the function body
    - In the case of block expressions, the scope for the block and the expressions are the same scope.
- Each arm of the `match` expression

Drop scopes are nested within each other as follows.
When multiple scopes are left at once, such as when returning from a function, variables are dropped from the inside outwards.
- The entire function scope is the outer scope
- The functon body block is contained within the scope of the entire function.
- The parent of the expression is an expression statement is the scope of the statement.
- The parent of the expression of a variable declaration is the declaration's scope.
- The parent of the statement is the scope of the block that contains the statement.
- The parent of the expression for a `match` guard is the scope of the arm that the guard is for.
- The parent of the experssion after the `=>` in a `match` is the scope of the arm it's in.
- The parent of the arm scope is the scope of the `match` expression that it belongs to.
- The parent of all other scopes is the cope of hte immediately enclosing expression.

##  Scopes of function parameters [↵](#destructors)

All function paramters are in the scope of the entire function, so are dropped last when evaluating the function.
Each actual function parameter is dropped after any bindings introduced in that parameter's pattern.

> _Todo_: Example

### 11.7.3. Scopes of local variables [↵](#destructors)

Local variables declared in a variable declaration are associated to the scope that contains the declaration.
Local variables declared in a `match` expression are associated to the arm scope of the `match` that they are declared in.

> _Todo_: Example

If multiple patterns are used in the same arm of a `match` expressions, then an unspecified pattern will be used to determin the drop order.

## Temporary scopes [↵](#destructors)

The temporary scope of an expressions is the scope that is used for the temporary variable that holds the result of he exprssion when used in a place context, unless it is promoted.

Apart from lifetime extensions, the temprory scope of an expression is the smallest scoped that contins the expression and is one of the following:
- The entire function.
- A statement.
- The body of an `if`, `while` or `loop` expression.
- The `else` block.
- The condition expressions of an `if` or `while` expression, or a `match` guard.
- The body expression for a `match` arm.
- The second operand of a lazy boolean operator.

> _Note_: Temporaries that are created in the final exprssion of a function body are dropped after any named variables bound in the function body.
> Their drop scope is the entire function, as tehre is no smaller enclosing temporary scope.
>
> The scrutinee of a `match` expression is not a temporary scope, so temporaries in the scrutinee can be dropped after the `match` expression.
> For example, the temporary for `1` in `match 1 { ref mut z => z };` lives until the end of the statement.

_TODO: Example_


## Operands [↵](#destructors)

Temporaries are also created to hold the result of operands to an expressions while the other operands are evaluated.
The temporaries are associated to the scope of the expressions with that operand.
Since the temporaries are moved from once the expreesssion is evaluated, dropping them has no effect unless one of the operands to an expression break out of he expression, returns, or panics.

_TODO: Example_

## Constant promotion [↵](#destructors)

Promotion of a value expression to a `static` slot occurs when the expression could be written in a constant and borowed, and that borrow could be dereferenced where the exprssion was originally written, without changing the runtime behavior.
That is, the promoted expression can be evaluated at compile-time and the resulting value does not contain [interior mutability](#115-interior-mutability-) or [destructors](#destructors) (these properties are determined based on the value when possible).

## Temporary lifetime extension [↵](#destructors)

> _Note_: This is subject to change

The temporary scopes for expressions in variable declarations are sometimes extended to the scope of the block containing the declaration.
This is done wherer the usual temporary scope would be too small, based on syntactic rules.

If a borrow, dereference, field, or tuple expression has an extended temporary scope, the nteh indexed experssions also has an extended scope.

## Extending based on patterns [↵](#destructors)

An extending pattern is either:
- An identifier pattern that binds by refernce or mutable reference.
- A struct, tuple, tuple struct, or slice pattern where at least one of the direct subpatterns in an extending pattern.

So `ref x`, `V(ref x)` and `[ref x, y]` are all extending patterns, but `x`, `&x` and `&(ref x, _)` are not.

If the pattern in a variable declaration is an extending pattern, then the temporary scope of the initializer expression is extended.

## Extending based on expressions [↵](#destructors)

For a variable declaration with an initializer, an extending expression is an experssion whici is one of the following:
- The initializer expression.
- The operand of an extending borrow experssion.
- The operand of an extending array, cast, braced struct, or tuple expression.
- The final expression of any extending block expression.

So the borrow expression is `&mut 0`, `(&1, &mut 2)`, and `Some{ 0: &mut 3 }` are all extending expressions.
The borrows in `&0 + &1` and `Some(&mut 0)` are not: the latter is syntactically a function call expression.

The operand of any extending expression has its temporary scope extended.

## Not running destructors [↵](#destructors)

`forget` can be used to prevent the destructor of a variable from being run, `ManuallyDrop` provides a wrapper to prevent a variable or field from being dropped automatically.

> _Note_: Preventing a destructor from being run via `forget` or other means is safe even if the type isn't static.
> Besides the place where destructors are guaranteed to run as defined by this document, types may not safely rely on a destructor being run for soundness.
