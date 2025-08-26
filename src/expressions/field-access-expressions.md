# Field access expressions
```
<field-access-iden> := <expr-path-ident> | <path-disambig>
<field-access-expr> := <expr> ( '.' | '?.' ) <field-access-iden>
```

A field expression is a place expression that evaluates to the location of a field of a struct or union.
When the operand is mutable, the field expression is also mutable.

If the `?.` access is uses, the field will only be accessed if the value it is called on is a non 'err' value, otherwise it will propagate this value.
The `?.` access is supported on any type that implements the `OptAccess` trait.

If a field expression is followed by an opening parenthesis, this would interpreted as a method call expression.

## Automatic dereferencing [↵](#field-access)

If the type of the left-hand-side operand implements `Deref` or `DerefMut` depending on whether the operand is mutable, it is automatically dereferenced as many times as necessary to make the field access possible.
This process is also called 'autoderef' for short.

## Borrowing [↵](#field-access)

The field of a struct or a reference to a struct are treated as separate entities when borrowing.
If the struct does not implement `Drop` and is stored in a local variable, this also applies to moving out of each of its fields.
This also does not apply if automatic dereferencing is done through user defined types that don't support this.
