# Constraints
```
<constraint-item> := { <attribute> }* [ <vis> ] 'constraint' <name> [ <generic-params> ] '{' <constriant-memeber> '}'
<constraint-members> := { <constraint-member> }*
<constraint-member>  := <constraint-fn>
                      | <constraint-method>
                      | <constraint-alias>
                      | <constraint-constant>
                      | <constraint-property>
                      | <disambig-alias>
                      | <expr> ';'
```

A constraint defines a set of bounds and items that a type must implement to adhere to it.
It allows for specialization on types without requiring them to explicitly implement anything, just needing to match any of the constraint's members.

Contraints may include additional generic arguments to refine any constriants.

A constraint has access to both a `Self` type and a `self` value (to use in a constraint expression).
These 2 values are limited to be accessed by only any items that the bounds bring into scope.


> _Example_
> ```
> constraint Foo {
> 
>     // requires `Self` to have an associated function named `foo`, returning an `i32`
>     fn foo() -> i32;
> }
> ```

A constraint expressions may have expressions which do not compile for certain associated types, this will result in the contraint not matchinge

> _Example_
> ```
> constraint Foo {
>     // requires `self` to have at least 1 tuple field, any non-tuple values will fail, as they do not have a field named '0'
>     self.0 is i32;
> }
> ```

## Inline constraints [↵](#constraints)
```
<inline-constaint> := 'constraint' '{' <constraint-members> '}'
```

In addition to named constraint, it's also possible to define an inline constraint.

Unlke regular constraints, they cannot have an generics, but are allowed to access any generic or const values from the scope they are defined in.

> _Example_
> ```
> fn foo[T]() {
>     if T is constaint { fn foo(); } {
>         // ...
>     }
> }
> ```