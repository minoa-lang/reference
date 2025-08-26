# Match expressions
```
<match-expr> := [ <label> ] `match` <expr> '{' { <match-case> }* <final-case> '}'
<match-case> := [ <label> ] <pattern> '=>' ( <expr> ',' | <block> [ ',' ] )
<final-case> := [ <label> ] <pattern> '=>' ( <expr> [ ',' ] | <block> [ ',' ] )
<scrutinee>  := ? <expr> except <struct-expr> ?
```

A `match` expression branches based on a value of a scrutinee.
The value of which will then be checked compared to the patterns provided within each branch.
The scrutinee and all expression must have the same type.

A `match` behaves differently depending on whether or not the scrutinee expression is a place or value expression.
Place expression will immediatelly be matched, while value expressions will first be evaluated to a temporary location.
This value is subsequently compared to the patterns in the arms until a match is found.
The first arm with a matching pattern is chosen as the branch target of the `match`, any variables bound by the pattern are assigned to local variables in the arm's block, and control enters the block.

When the scrutinee is a place expression, the match does not allocate a temporary location; however, a by-value binding may copy or move from the memory location.
When possible, it is preferable to match on place expressions, as the lifetime of these matches inherits the lifetime of the place expression rather than being restricted to the inside of the match.

Variables bound within the pattern are scoped to the match guard and the arm's expression.
The binding mode (move, copy, or reference) depends on the pattern.

Multiple match patterns may be joined with the `|` operator.
Each pattern will be tested in a left-to-right sequence until a successful match is found

Every binding in each `|` separated pattern must appear in all of the patterns in the arm.
Every binding of the same name must have the same type, and have the same binding mode.

### 9.20.1. Fallthrough labels [↵](#match-expressions)

A pattern is allowed to have a label.
A label may only be referenced by a `fallthrough` expression within an arm of the `match` expression.
This will then proceed to evaluate another arm in the `match`.

Labels are only allowed if the arm does not capture any bindings.
