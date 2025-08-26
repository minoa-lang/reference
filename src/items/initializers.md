# Initializers
```
<init-item> := <fn-qualifier> 'init' [ '?' | '!' <type> ] 'fn' [ <deduced-params> ] '(' <fn-params> ')' [ <where-clause> ] { <contract> }* <block>
```

An initializer, also known as a init function is a special kind of function that allows for the construction of a type.
Even though they are functions, they act very differently to most functions and therefore have their own section dedicated to them.

An initializer has an implicit return type of `Self` by default, this may be overwritten with either an optional or result type.

An initializer has a set of deduced and regular parameters like a normal function.

Multiple initializers are allowed, but they must be distinguishable by there required parameters, similar to [label-based overloading](../items/functions.md#label-based-overloading-).

Initializers act as if they are ran on a `&mut MaybeUninit(Self)`, and can therefore also be used with an [in-place operator](../operators/special-operators.md#in-place-operator-).
Initializers are also allowed to assign any immutable value within the type they initialize, similar to [constructing expressions](../expressions/constructing-expressions.md).

## Default initialization [↵](#initializers)

Normally, an initializer is required to assign a value to all fields within the type, or it will result in an error.

To help avoid the need to writing repetitive code in different initializer, the initializer can use, so called, default initializion.

When at the end of an initializer, a field has not been assigned, it can do one of two things:
- If a default value is defined for a field and the field will be set to that value
- If a field has a type that implements `Default`, the field will get the value returned by it.

## Initializer delegation [↵](#initializers)

Initializers can themselves call different initializers, this is called initializer delegation, allow for the re-use of code.

This can be done by calling an initializer of `Self` within the initializer.
This notifies to the compiler to not try and default initialize any other members, and that it is allowed to not fully initialize the item.

If a field would normally get its value for a default initialization, but is assigned in the initializer calling that other initializer, the default assignment will be skipped.

> _Note_: The call needs to be explicitly on `Self`, and not just its type

## Fallible initializers [↵](#initializers)

Initializers can be fallible, meaning they might fail to initialize.

There are 2 possible variants of fallible initializers:
- `init?`: returns `None` when failing to initialize
- `init!type`: returns an `Err(...)` when failing to initialize. The `!` is followed by the type of the error returned when it fails.

Calling these initializers is done as `T?` or `T!`, where `T` represents the type to initialize.
