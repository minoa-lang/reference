# Method expressions
```
<method-call-expr> := <expr> '.' <iden> <func-args>
```

Method calls are used to call any associated method, either as direct methods or trait method implementations.
They are done by statically dispacthing to a method if the exact slef-type of the left-hand side is known, or dynamically dispatching if the left-hand side expression is a trait object.

Each argument can have an additional function argument label in case the function requires one.
Any default arguments do not need to be provided and will be evaluated after evaluating the supplied operands, in the order they were defined in the signature.

Like function calls, the method calls can call optional trait methods.

## Method lookup [↵](#method-call-expression)

When looking up a method call, the receiver may be automatically dereferenced or borrowed in order to call a method.
This requires a more complex lookup process than for other functions, since there may be a number of possible methods to call. The following procedure is used:

1. Build a list of candidate receiver types.
   1. Obtained by repeatedly dereferencing the receiver's type, adding each type encountered to the list.
   2. Finally attempt an unsized coercion at the end, and adding the result type to the candidate list if that is successful.
   3. Then for each candidate `T`, add `&T` and `&mut T` to the list immediately after `T`.
2. Then for each candidate type `T`, search for a visible method with a receiver of that type, and arguments matching the provided labels, in the following places.
   1. `T`'s inherent methods (methods implemented directly by T).
   2. Any of the methods provided by a visible interface implemented by `T`.
      If `T` is a type parameter, methods provided by interface bounds on `T` are looked up first.
   3. All remaining methods scopes are looked up.
3. Pick the methods matching the arguments.

> _Note_: more detailed info about argument resolution and conflicts, check the [function definition item]

If this results in multiple possible candidates, it is an error, and the receiver must be converted to an appropriate receiver type to make the method call.

This process does not take into account the mutability of the receiver, or whether a method is `unsafe`.
Once a method is looked up, if it can't be called for one (or more) of those reasons, it will result in a compiler error.

If a step is reached where there is more than one possible methods such as where generic methods or interfaces are considered the same, then it is a compiler error.
These cases require a disambiguating as the methods identifier.



[function definition item]: ../items/functions.md