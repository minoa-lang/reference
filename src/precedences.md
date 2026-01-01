# Precedences

Precedences are used to define how operator expressions are evaluated relative to each other.
These recedences can only be used for [infix operators].

Each precedence controls 2 different things:
- the order in which operators are evaluated
- when chained, which side should be evaluated first

The order is defined in terms of higher and lower precedences, i.e. before and after which other operators it should be evaluated.

The side to evaluate first on the other hand, is defined by the associativity of the precedence.

> _Example_
> 
> The expression `a + b + c` could be written as either `(a + b) + c` or `a + (b + c)`.
> 
> While this might not always impact the eventual result, certain operation are not communitative, which would result in different result depending on the order that is used.
> In addition, the order can also have an impact on the type, where changing the order might result in a commpilation error.

> _Note_: This section only specifies precedences when it comes to operators, all other expressions's precedences are defined within the [expression's section].

## Precedence items [↵](#precedences)
```
<precedence>        := 'precedence' <name> '{' ? { <precedence-member> }* , each element must be unique ? '}'
<precedence-member> := `higher_than` ':' <name> { ',' <name> }* ';'
                     | `lower_than` ':' <name> { ',' <name> }* ';'
                     | `associativity ':' ( 'left' | 'right' | 'none' ) ';'
```

Precedence items are used to define a new precedence.

### Precedence order [↵](#precedence-items-)

Each item can define its order relative to other precedences using the `higher_than` and `lower_than` members.
These 2 members are given a list of other precedences to which they must be relative to.

All precedences passed to `higher_than` must be of a lowr precedence than those defined in `lower_than`.
Additionally, they must not result in a cyclical dependency on each other.

If no explicit `higher_than` or `lower_than` is provided, and no other precedence links to it, these values will be assigned as `Lowest` and `Highest` repectively.

It is possible for 2 precedences to not have a linear relation to each other, meaning that one does not link to the other in the resulting precedence graph (ignoring `Lowest` and `Highest`).
In this case, when both are used within an expression, parentheses must be used to explicitly define the order in which they need to be resolved.

> _Example_
> using the following relation between precedences
> ```
> Highest
>  / \
> A   |
> |   C
> B   |
>  \ /
> Lowest
> ```
> The operators with precedence `A` or `B` cannot be used with one of precedence `C`, unless explicit parentheses are used.
> 
> Meaning that `v0 A v1 B v1` is allowed, but both `v0 A v1 C v1` or `v0 C v1 B v1` are not.
> 
> These need be be explicitly written as either `(v0 A v1) C v1` or `v0 A (v1 C v1)`, and `(v0 C v1) B v1`  or `v0 C (v1 B v1)`

### Associativity [↵](#precedence-items-)

The associativity of the precedence is define using the `associativity` member.

This member may be one of the following values:
- `left`: the operators will be evaluated from left to right
- `right`: the operators will be evaluated from right to left
- `none`: the operators need explicit parentheses when chained

In no explicit associativity is set, it will default to `none`.

> _Example_
> 
> Assuming the expression `a op b op c`, the expression will result in the following depending on the associativity:
> - `left`: `(a op b) op c`
> - `right`: `a op (b op c)`
> - `none`: requires explicit parentheses or will produce an error

## Core precedences [↵](#precedences)

The core precedences are a set of precedences which are provided by the `core` library, which form the basis of all other user-defined precedences.

Below is a table of the provided core precedences, their properties, and the operators they apply to

precedence  | lower than  | associativity | associated operators
------------|-------------|---------------|------------------------------------------------------------------------
`Highest`   | n/a         | n/a           | none, this indicates the highest precedence in the precedence hierachy
`PowRep`    | `Highest`   | left to right | `**` ([power/repetition])
`MulDivRem` | `PowRep`    | left to right | `*` ([multiply]), `/` ([division]), and `%` ([remainder])
`AddSub`    | `MulDivRem` | left to right | `+` ([addition]) and `-` ([subtraction])
`ShiftRot`  | `AddSub`    | left to right | `<<` & `>>` ([bitwise shift]), and `<<*` & `>>*` ([bitwise rotate])
`BitAnd`    | `ShiftRot`  | left to right | `&` ([bitwise AND])
`BitXor`    | `BitAnd`    | left to right | `~` ([bitwise XOR])
`BitOr`     | `BitXor`    | left to right | `\|` ([bitwise OR])
`Select`    | `BitOr`     | left to right | `?:` ([or-else]) and `??` ([catch])
`Compare`   | `Select`    | left to right | `==`, `!=`, etc ([comparison])
`Contains`  | `Compare`   | left to rifht | `in` and `!in` ([contains])
`LogicAnd`  | `Compare`   | left to right | `&&` ([logical AND])
`LogicOr`   | `LazyAnd`   | left to right | `\|\|` ([logical OR])
`Range`     | `LogicOr`   | left to right | `..` & `..=` ([range])
`Pipe`      | `Range`     | left to right | `<\|` & `\|>` ([pipe])
`Lowest`    | `Pipe`      | n/a           | none, this indicates the lowest precedence in the precedence hierachy

## Precedence scoping and use
```
<precedence-use>  := 'precedence' 'use' <use-root> [ <precedence-path> ] ';' 
<precedence-path> := '.' <name>
                   | '.' { <name> { ',' <name> }* [ ',' ] }
```

Predences have different scoping rules to other symbols, as they are not relative to any module or item, but are only relative to the [main module].
Meaning that all precedences are located into a single namespace.

This also requires precedences to be imported using a special variant of the [use item].
This variant may either import all precedences from a given library, or specify individual precedences to import.
This variant in only allowed to be located within the [main module], and may not be located in any other item.

This is done to ensure the use of a given precedence stays consistent across an entire library.

The `core` precedences are imported by default via the `core`'s prelude.



[expression's section]: ./expressions.md#expression-precedence--operator-evaluation-
[use item]:             ./items/use.md
[power/repetition]:     ./operators/core-operators.md#arithmetic-
[multiply]:             ./operators/core-operators.md#arithmetic-
[division]:             ./operators/core-operators.md#arithmetic-
[remainder]:            ./operators/core-operators.md#arithmetic- 
[addition]:             ./operators/core-operators.md#arithmetic-
[subtraction]:          ./operators/core-operators.md#arithmetic-
[bitwise AND]:          ./operators/core-operators.md#bitwise-
[bitwise XOR]:          ./operators/core-operators.md#bitwise-
[bitwise OR]:           ./operators/core-operators.md#bitwise-
[bitwise shift]:        ./operators/core-operators.md#bitwise-
[bitwise rotate]:       ./operators/core-operators.md#bitwise-
[or-else]:              ./operators/core-operators.md#or-else-
[catch]:                ./operators/special-operators.md#catch-
[comparison]:           ./operators/special-operators.md#comparison-
[contains]:             ./operators/special-operators.md#contains-
[logical AND]:          ./operators/special-operators.md#logical-and-
[logical OR]:           ./operators/special-operators.md#logical-or-
[range]:                ./operators/core-operators.md#core-operators
[pipe]:                 ./operators/core-operators.md#core-operators
[infix operators]:      ./operators.md#operator-kinds-
[main module]:          ./package-structure.md#main-module-