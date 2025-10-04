# Destructors

When an initialized variable or temporary goes out of scope its destructor is run, or it is _dropped_.
Assignment also runs the destructor of its left-hand operand, if it's initialized.
If a variable has been partially initialized, only its initialized fields are dropped.

The destructor of a type `T` constist out of:
1) If `T is Drop`, calling `T.(Drop.drop)()`
2) Recursively running the destructor of all its fields
    - The fields of the following are dropped in declaration order:
      - [`struct`s]
      - [tuple `struct`s]
      - [`enum`s]
      - [tuples]
    - the elements of an [array] or owned [slice] are dropped in order of index
    - the variables captured by move in a [closure] are dropped in an unspecified order
    - [trait objects] run the destructor of the underlying type
    - Other type don't result in any further drops

If a destructor must be run manually, such as when implementing a smart pointer, [`drop_in_place`] can be used.

Dropping a value more than once, it will result in [illegal behavior].

## Drop scopes [↵](#destructors)

Each variabble or termporary is associated with a certain _drop scope_.
When control flow leaves a drop scope, all variables associated with that drop scope are dropped in the reverse order than they were declared in.
This works similar for temporaries, where the order is the reverse of how they were created.

Values are only dropped after all [`defer` statements] have executed.

Overloaded or user-defined operators are not distinguished from built-in operator.
[Binding modes] are also not considered when dropping.

For any given function or closure, the following drop scopes exists:
- The entire function/closure
- each [statement]
- each [expression]
- each block, including the function/closure body
  - in case of a [block expression], both the exprssion and scope are the same _scope_.
- Each arm of a [`match` expression]
- Each pattern binding and implicit result binding in an [`if` expression], [`while` expression], or [`for` expression].

Drop scopes are nested within each other in the following order.
When multiple scopes are left at once, such as when returning from a function, variables are dropped frm the inside outwards.
- entire function body, being the outer scope
- function body block contained within the entire function
- parent of an expression in an [expression statement] is the scope of the statement
- parent of the initializer of a [variable declaration] is the declaration's parent
- parent of a statement scope is the scope of hte block that contains the statement
- parent of an expression in a [guard pattern] is:
  - the arm of a [`match` expression]
  - the pattern binding and implicit result binding in an [`if`], [`while`], or [`for` expression]
- parent of a the expression following the `=>` in a [`match` expression] arm, is the scope of the arm
- parent of the `match` arm scope is the scope of the [`match` expression] it belongs to
- parent of all other scopes is hte scope of hte immediatelly enclosing expression

## Scopes of function parameters [↵](#destructors)

All function paramters are in the scope of the entire function, so are dropped last when evaluating the function.
Each actual function parameter is dropped after any bindings introduced in that parameter's pattern.

> _Example_:
> ```
> // Drops `y`, then the second parameterd, then `x`, and finally the first parameter
> fn patterns_in_params(
>     (x, _): (PrintOnDrop, PrintOnDrop),
>     (_, y): (PrintOnDrop, PrintOnDrop),
> );
> 
> // drop order: 3, 2, 0, 1
> fn pattersn_in_params(
>     (PrintOnDrop(0), PrintOnDrop(1))
>     (PrintOnDrop(2), PrintOnDrop(3))
> )
> ```

## Scopes of local variables [↵](#destructors)

Local variables declared in a [variable declaration] are associated to the scope that contains the declaration.

> _Example_
> ```
> declared_first := PrintOnDrop("Dropped last in outer scope (2)");
> {
>     declared_in_block := PrintOnDrop("Dropped in inner scope (0)");
> }
> declared_fist := PrintOnDrop("Dropped first in outer scope (1)");
> ```

When declared in a [`match` expression], they are associated to the arm scope of the `match` that they are declared in.

> _Example_
> ```
> PrintOnDrop("Dropped in outer scope (2)")
> match PrintOnDrop("Dropped at end of match (1)") {
>     _ => PrintOnDrop("Dropped at end of arm, before match (0)")
> }
> ```

If multiple patterns are used in the same arm of a `match` expressions, then an unspecified pattern will be used to determin the drop order.

## Temporary scopes [↵](#destructors)

The _temporary socpe_ of an expression defines the scope that is used for temporary variables that hold the result of that expression when used in a [place context], unless [promoted].

With the exception of lifetime extension, the temporary scope of an expression is hte smallest scope that constains the expressions and is one of the following:
- the entire function
- A statement
- body of an [`if`], [`loop`], [`while`], and [`do`-`while`] expressions
- `else` block of any of the above expressions
- non-pattern matching expression of an [`if`], [`while`], [`do`-`while`] expression
- expressions within a [guard pattern]
- body expression for a `match` arm
- Arguments of [lazy parameters] and [unwrap parameters]
- Operands of [lazy operators]
- [let-bindings] in conditions
- tail expression of a block
- the error value of a [result-`if`], [result-`while`], or [result-`for`] expression

> _Note_: The [scrutinee] of a match expression is not a temporary scope, so temporaries in the scrutinee can be dropped after the match expression.
>         For example, the temporary 1 in `match 1 { ref mut val => val };` lives until the end of the statements the match expression is in


> _Example_
> ```
> local_var := PrintOnDrop("local var");
> 
> // Dropped once the condition has been evaluated
> if PrintOnDrop("If condition").0 == "If condition" {
>     // Dropped at the end of the block
>     PrintOnDrop("If body");
> } else {
>     #unreachable
> }
> 
> if let "If let scrutinee" = PrintOnDrop("If let scrutinee").0 {
>     PrintOnDrop("If let consequence").0
>     // "if let consequence" dropped here
> }
> // "If let scrutinee" dropped here
> else {
>     PrintOnDrop("If let else").0
>     // "If let else" dropped here
> }
> 
> if PrintOnDropResult("Ok condition", "Err condition") {
>   PrintOnDrop("If ok consequence").0;
>   // "If ok consequence" dropped here
> }
> // "Ok conditon" dropped here
> else (err) {
>   PrintOnDrop("If err consequence").0;
>   // "If err consequence" dropped here
> }
> // "Err condition" dropped here
> 
> while let x = PrintOnDrop("while let scrutinee").0 {
>     PrintOnDrop("Loop body");
>     // "Loop body" dropped here
>     // "While let scruntinee" dropped here
> }
> 
> fn foo(lazy x: &str) {
>     if CONST_VAL {
>         // x is dropped at the end of the function
>         x == "";
>     } else {
>         // x's value is never created, so not dropped either
>         ();
>     }
> }
> 
> foo(PrintOnDrop("Lazy argument").0);
> 
> // Dropped before first ||
> (PrintOnDrop("first operand") ||
>  // Dropped before )
>  PrintOnDrop("second operand")) ||
> // Dropped before ;
> PrintOnDrop("third operand");
> 
> // Scrutinee is dropped at the end of hte function, before local variables
> // (because this is the tail expression of the function block body)
> match PrintOnDrop("match value in final expression") {
>     // Dropped once the condition has been evaluated
>     _ if PrintOnDrop("guard pattern condition").0 == "" => (),
>     _ => (),
> }
> ```

## Operands [↵](#destructors)

Temporaries are also created to hold the result of operands to an expression, while the other operands are evaluated.
The temporaries are associated to the scope of hte expression with that operand.
Since the temporaries are moved from once the expression is evaluated, dropping them has no effect unless one of the operands to an expression breaks out of the expression, returns, or panics.

_Example_
```
loop {
    // Tuple expression doesn't finish evaluating, so operands drop in reverse order
    (
        PrintOnDrop("Outer tuple first"),
        PrintOnDrop("Outer tuple second"),
        (
            PrintOnDrop("Inner tuple first"),
            PrintOnDrop("Inner tuple second"),
            break,
        ),
        PrintOnDrop("Never created, since the loop breaks in the above tuple")
    )
}
```

## Constant promotion [↵](#destructors)

Promotion of a value expression to a constant slot occurs when the expression could be written in a constant and borrowed, and that borrow could be dereferenced where the expression was originally written, without chaning the runtime behavior.
That is, the promoted expression can be evaluated at compile-time and the resulting value does not cotnain [interior mutability] or [destructors] (these properties are determined based on the value where possible, e.g. `&null` always has the type `&?_` with a static lifetime, as it contains nothing disallowed)

## Temporary lifetime extensions [↵](#destructors)

> _Note_: This is subject to change

The temporary value for expressions in [variable declarations] are sometimes extended to the scope of the block cotnaing the declaration.
This is done when the usual temporary scope would be too small, based on certain syntactic rules.

> _Example_
> ```
> x := &mut 0;
> // Usually a temporary would be dropped by not, but the temprary `0` lives until the end of the block
> println("{}", x);
> ```

Lifetime extension also applies to `static` and `const` items, where it makes temporaries live until the end of the program.

> _Example_
> ```
> const C: &DynArr(i32) = DynArr.new();
> // Usually this would be a dangling reference, as the `DynArr` would only exists inside the initializer expression of `C`,
> // but instead the borrow gets lifetime-extended so it effectively has a static lifetime
> println("{:?}", C);
> ```

If a [borrow], [dereference], [field], of [tuple index expression] has an extended temporary scope, the so does the operand.
If an [index expression] has an extended temporary scope, the the indexed expression also has an extended temporary scope.


## Extending based on patterns [↵](#temporary-lifetime-extensions-)

An extending pattern is either
- An [identifier pattern] that binds a reference or mutable reference
- If one of the following contains at least one pattern of the directl subpatterns is an extending patter:
  - [struct pattern]
  - [tuple pattern]
  - [tuple struct pattern]
  - [slice pattern]

> _Example_: So `ref x`, `S(ref x)`, and `[ref x, y]` are all extending patterns, but `x`, `&ref x`, and `&(ref x,)` are not.

If a pattern in a [pattern variable declaration] is an extending pattern, then the temporary scope of the initializer expression is extended


### Extending based on expressions [↵](#temporary-lifetime-extensions-)

When inside of the initializer for a variable declaration, an exteding expressions is an expression which is one of the following:
- initializer expression
- operand of an extending [borrow]
- operands(s) of one of the following extending expressions
  - [array expression]
  - [struct expression]
  - [tuple expression]
  - [type cast expression]
- arguments to an extending [tuple `struct`] or [tuple variant] initializer
- final expressions of any extending [block expression]

Meaning that hte borrow expressions in `&mut 0`, `(&1, &mut 2)`, and `.Some(&mut 3)` are all extending expressions, but the borrows in `&0 + &1` and `f(&mut 0)` are not.

The operatns of any extending borrow expression has its temprary scope extended.

_Examples_
Here are some examples where expressions have extended temporary scopes
```
// The temporary scope of the result of `temp()` lives in the same scope as `x` in these cases
x := &temp();
x := &temp() as &dyn Clone;
x := (&*temp(),);
x := { [.Some(&temp())] };
let ref x = temp();
let ref x = *&temp();
```
And some examples whre expressions don't have their temprary scopes extended:
```
// The temprary that stores the result of `temp()` only lives until the end of te variable declararion
x := identity(temp()); // error
x := (&temp).do_something(); // error
```

## Not running destructors [↵](#destructors)

In some situation, it can be useful to prevent the destructor running at all.

### Manually supressing destructors [↵](#not-running-destructors-)

By using [`forget`], the compiler can be explicitly told to not run the destructor of a type.

A type can also be wrapped within a [`ManuallyDrop(T)`] wrpapper that automatically prevents a value from being dropped, while still allowing it to be dropped manually.

> _Note_: Because of this, it is not possible to rely on a destructor being run for soundness

### Process termination [↵](#not-running-destructors-)

Since there is currently no support for unwinding, destructors are never run on a panic.

## Partial destruction [↵](#destructors)

Since a destructor will drop all fields in a type, it would cause an issue when a value is moved out of the type.
Because of this, the compiler supports partial destruction.
Meaning that any fields that are moved out of the type will not have their destructors called.

This requires that all values that are moved out of the type are visible within the same function from a compiler standpoint, as it needs this info to be able to correctly run the destructors for the required fields only.



[destructors]:                  #destructors
[`drop_in_place`]:              #destructors "Todo: link to docs"
[`forget`]:                     #destructors "Todo: link to docs"
[`ManuallyDrop(T)`]:             #destructors "Todo: link to docs"
[promoted]:                     #constant-promotion-
[interior mutability]:          ./interior-mutability.md
[`enum`s]:                      ./types/composite-types/enum-types.md
[tuple variant]:                ./types/composite-types/enum-types.md#tuple-variants-
[`struct`s]:                    ./types/composite-types/struct-types.md
[tuple `struct`s]:              ./types/composite-types/tuple-struct-types.md
[tuple `struct`]:               ./types/composite-types/tuple-struct-types.md
[tuples]:                       ./types/composite-types/tuple-types.md
[closure]:                      ./types/function-like-types/closure-types.md
[array]:                        ./types/sequence-types/array-types.md
[slice]:                        ./types/sequence-types/slice-types.md
[trait objects]:                ./types/trait-types/trait-object-types.md
[expression]:                   ../expressions.md
[place context]:                ../expressions.md#place-expressions-
[block expression]:             ../expressions/block-expressions.md
[array expression]:             ../expressions/constructing-expressions/array-expressions.md
[struct expression]:            ../expressions/constructing-expressions/struct-expressions.md
[tuple expression]:             ../expressions/constructing-expressions/tuple-expressions.md
[field]:                        ../expressions/field-access-expressions.md
[`if`]:                         ../expressions/if-expressions.md
[let-bindings]:                 ../expressions/if-expressions.md#let-bindings-
[result-`if`]:                  ../expressions/if-expressions.md#result-if-expressions-
[index expression]:             ../expressions/index-expressions.md
[`match` expression]:           ../expressions/match-expressions.md
[scrutinee]:                    ../expressions/match-expressions.md#scrutinee- "Todo: Section does not exists yet"
[`do`-`while`]:                 ../expressions/loop-expressions.md#do-while-else-
[`for` expression]:             ../expressions/loop-expressions.md#for-expression-
[result-`for`]:                 ../expressions/loop-expressions.md#result-for-
[`loop`]:                       ../expressions/loop-expressions.md#loop-expression-
[`while`]:                      ../expressions/loop-expressions.md#while-expression-
[result-`while`]:               ../expressions/loop-expressions.md#result-while-
[tuple index expression]:       ../expressions/tuple-index-expressions.md
[type cast expression]:         ../expressions/type-cast-expressions.md
[illegal behavior]:             ../illegal-behavior.md#multiple-drops-
[lazy parameters]:              ../items/functions.md#lazy-parameters-
[unwrap parameters]:            ../items/functions.md "Todo: link to section"
[statement]:                    ../statements.md
[expression statement]:         ../statements/expression-statements.md
[`defer` statements]:           ../statements/defer-statements.md
[variable declaration]:         ../statements/variable-declarations.md
[variable declarations]:        ../statements/variable-declarations.md
[pattern variable declaration]: ../statements/variable-declarations.md#pattern-variable-declaration- "Todo: Section does not exists yet"
[identifier pattern]:           ../patterns/identifier-patterns.md
[Binding modes]:                ../patterns/identifier-patterns.md#binding-modes-
[guard pattern]:                ../patterns/guard-patterns.md
[slice pattern]:                ../patterns/slice-patterns.md
[struct pattern]:               ../patterns/struct-patterns.md
[tuple pattern]:                ../patterns/tuple-patterns.md
[tuple struct pattern]:         ../patterns/tuple-struct-patterns.md
[lazy operators]:               ../operators.md#lazy-
[borrow]:                       ../operators/special-operators.md#borrow-operators-
[dereference]:                  ../operators/special-operators.md#derefence-operator-