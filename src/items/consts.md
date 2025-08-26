# Consts
```
<const-item> := { <attribute> }* [ <vis> ] 'const' <name> [ ':' <type> ] '=' <expr> ';'
```

A constant item is a named constant value which is not associated with a specific memory location, i.e. the value is known at compile time.
Contants are essentially inlined when they are used, meaning that they are copied directly into the relevant context when used.
This includes constants from other libraries and non-`Copy` items.

When a reference is taken to a constant value from different locations, they are not neccesarily guarenteed to point to the same memory location.

Constants are generally explicitly types, unless a certain sub-set expressions are used, these are:
- literal expression with a literal operator
- _TODO: others_

Constants live throught the entirety of the program and any reference to them is always valid.

Constants may be of types that have a destructor, and will be dropped when the copy of the value that they are assigned to go out of scope.

When defined inside of an implementation, the const item will be an associated with that type.

## trait constant [↵](#const-item)

```
<trait-const> := 'const' <name> ':' <type> [ '=' <expr> ] ';'
```

An trait constant declares a signature for an associated constant implementation, i.e. it declares both the name and the type the associated constant should have.

A default value can be provided which will be used when no explicit constant is defined within an implementation.
