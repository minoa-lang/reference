# Type check expressions
```
<type-check-expr> := <expr> <is-op> <type>
<is-op> := 'is' | '!is'
```

A type check expression is a special binary operator, which has a type on the right hand side.
A type check expression can be used for the following:
- if an impl trait object, or a generic is of a given type
- if a trait object is a given type, when runtime type info is enabled
- if an object, reference to an object, or a pointer to an object, implements a given interface

This check can only occur on place expressions.

There is both a positive and negative version of this expression.

When the positive version is used in the condition of a conditional expression, and it is the only type check experssion on this value, the value will be implicitly promoted within the block that gets executed when the condition is true.

Any check that happens on a generic or impl interface object will be resolved at compile time.
