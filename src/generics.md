# Generics

Generics allows for the reuse of items and types, by writing generalized version which can be _parameterized_ using compile-time parameters.

Generic parameters can be provided in 2 ways:
- as a specific list of parameters following any non-function items, and types
- as [deduced] and constant parameters in a [function]

Creating an instance of a generic item/type is known as _instantiation_ or _monomorphization_, both of these terms can be used interchangably.

## Generic parameters [↵](#generics)
```
<generic-params>     := '[' <generic-param> { ',' <generic-param> }* [ ',' <parameter-pack> ] ']'
                      | '[' <parameter-pack> ']'
<generic-param>      := <name> [ <generic-type-kind> ] 
                      | <name> [ <generic-param-type> ] [ '=' <expr> ]
                      | <parameter-pack>
<generic-param-type> := <generic-type-kind>
                        <generic-value-kind>
```

Generic parameters define what values need to be provided for a generic item/type to be instantiated.
They indicate the kind of the generic parameter, either type or value, any bounds, and an optional default value.

If no type is provided for a generic parameter, a type of `type` will be assumed.

### Optioal parameters [↵](#generic-parameters-)

When a default value is provided for a generic parameter, they are known as optional generics.
Meaning that when no explicit value is given when instantiating a generic value, the declared value will be used instead.

When a generic parameter is provided outside of a function, they must be located after all non-optional generic parameters.

If any optional generic parameter is located after another, the generic arguement can either be:
- explicitly specified with the default value of the corresponding generic parameter
- implicitly inferred from the generic parameter by providing it with a value of `_`

> _Example_
> 
> If we have the generic type
> ```
> struct Foo[T = i32, U = i32] {}
> ```
> And we need to pass an `f64` to the second parameter, we can do it in the following ways
> ```
> // Pass the first optional generic's default value explicitly
> type A = Foo[i32, f64];
> 
> // Let the first optional generic's value be automatically inferred from its definition
> type B = Foo[_, f64];
> ```

## Generics in functions [↵](#generics)

Defining generic parameters in a function is handled via either:
- constant function parameters
- [deduced] function parameters

Since these parameters act like generic parameters, the function can be though of a being a generic type,
where these arguments make up the generic parameters of the function, while any other parameters are the actual parameters on the call to to the function.

## Type generics [↵](#generics)
```
<generic-type-kind> := 'type'
                     | 'is' <type-bounds>
```

Type generics are generics which may receive any value of the type `type`, i.e. other types.

By default, type generics have a default bound to `Sized`.
This bound can be relaxed by explicitly providing either an optional or negative bound for `Sized`.

When deefining the type generic, it may directly be specified with a set of type bounds.
This is syntactic sugar which will move the bounds into the [`where`-clause] of the generic item/type.

> _Example_
> ```
> struct Foo[T] { ... }
> 
> // identical to the above one, but explicitly specifies the type
> struct Foo[T: type] { ... }
> ```

### Type bounds [↵](#type-generics-)
```
<type-bounds> := <type-bound> { '&' <type-bound> }
               | <concrete-type-bound>
<type-bound>  := [ '?' | '!' ] <path>
```

A type bound allows restriction on a generic type parameter being passed, while also allowing the associated functionality of that type to be able to be used.

Bounds can be done in 2 ways:
- as a bound contraining the type by a trait/constraint
- a list of allowed types types

#### Trait/constraint bounds [↵](#type-bounds-)
```
<trait-constraint-bounds> := <trait-constraint-bound> { '&' <trait-constraint-bound> }
<trait-constraint-bound>  := [ '!' | '?' ] <path>
                           | <inline-constraint>
```

A trait/constraint bounds allows a type to be restricted to implement a trait or conform to a constraint.

When a type is bound by traits or constraints, the bound comes in 1 of 3 forms:
- a positive bound: this requires a type to implement the given trait or conform to a constraint.
                    This allows the generic type to utilize any functionality defined within the trait or constraint, as if it was called on a regular type implementing these.

- an optional bound: This allows a type to relax a previously declared positive or negative bound.
                     This does **not** guarantee that any functionality will be available on the generic type.
                     A common use-case is to relax the default `Sized` bound, allowing [dynamically sized types] to be passed to the generic type parameter.
                     This cannot be used with a constraint.

- a negative bound: This requires that a type does **not** implement the given trait.
                    This cannot be used with a constraint.


> _Example_
> ```
> // restricts `Foo` to only be able to hold a type implementing `Bar`
> struct Foo[T is Bar] {
>     val: T,
> }
> 
> // allow `Foo` to hold any sized and unsized type
> struct Foo[T is ?Sized] {
>     val: T,
> }
> 
> // restrict `Foo` to only be able to hold a type that does not implement `Baz`
> struct Foo[T is !Baz] {
>     val: T,
> }
> ```

#### Concrete type bounds [↵](#type-bounds-)
```
<concrete-type-bound> := <type> { '|' <type> }*
```

When a generic type parameter is constraint by a concrete set of type.
This limits the parameters to only allows one of these types to be passed to it.

> _Example_
> ```
> // restricts Foo to only be able to be `Foo[i32]` or `Foo[u32]`
> struct Foo[T is i32 | u32] {
>     val: T,
> }
> ```

#### Associated type bounds [↵](#type-bounds-)
```
<assoc-type-bounds> := '.' <name> 'is' <type-bounds>
                     | '.' <name> '=' <type>
```

When a path is used within a bound, its generic arguments may contain an associate type bound.
The associate type bound allows, as the name implies, for a bound to be applied on an associated trait type.

They may be bound either by using type bounds, or an explicit type.

> _Example_
> ```
> // requires `T.(Add.Output)` to implement `Foo`
> struct Foo[T is Add(.Output is Foo)] {}
> ```

## Value generics [↵](#generics)

Value generics may have any type, other than `type`, which adheres to the following requirements:
- is a [builtin type]
- implements [`StructuralIdentity`]

## Parameter packs [↵](#generics)
```
<parameter-pack>      := <name> '...' [ ':' <generic-param-type> ]
                       | '(' <name> { ',' <name> }[N] ')' '...' [ ':' '(' <generic-param-type> { ',' <generic-param-type> }[N] ')' ]
```

A parameter pack represents a list of 0 or more instances of a parameter pack set.
They allows for an unknown amount of generic arguments to be passed to a single generic parameter.
They are required to be the last parameter within a generic parameter declaration.

When values are provided to a parameter pack, the number of argument must be a equal to an integer multiple of the number of sub-parameters.

A parameter pack set is a collection of sub-parameters, these can be represented in 2 ways:
- if only a single sub-parameter is needed, it is indicated with a simple name and an optional type
- if multiple sub-parameters are needed, they are written as a parenthesized list of names, with an optional parenthesized list of types.
  The number of types must correspond to the number of names.

The parameter pack can be provided with an optional pack description, which defined the type of each sub-element.
If no description is provided, all sub-elements will be default to type parameters.

A parameter pack can be used to represent a list of optional type parameters, but may also be provided with a default value.
This default value is a list of values, which have matching types as those defined within the description.
Similarly to providing values, the number of parameters provided must be equal an integer multiple of the number of sub-parameters.

> _Note_: Parameter packs in generics are the equivalent of [varaidic parameters] in functions

> _Todo_: Figure out ergonomics, e.g. number of parameters, looping over them, etc.
>         This will likely be provided by a set of metafunctions.

## Where clause [↵](#generics)
```
<where-clause> := 'where' <where-bound> { ',' <where-bound> }* [ ',' ]
<where-bound>  := <type> 'is' <type-bound>
                | <expr>
```

A where caluse is a collection of bounds that the generic parameters need to adhere to.
It contains all bounds that the generic argument being passed needs to adhere to when creating an instance of the item/type.

Bounds are a comma-separated list of the following:
- a type bound for generic type parameters
- a compile-time boolean expression that the value needs to adhere to for generic value parameters.

> _Implementation note_: Since types are expressions, and the `is` in the bound is basically a [type check expression], it will be parsed as such.

> _Example_
> ```
> struct Foo[T, N: usize] where
>     T is Bar,
>     N.is_power_of_two()
> {}
> ```

## Generic arguments [↵](#generics)
```
<generic-args>     := '[' <generic-arg> { ',' <generic-arg> } [ ',' ] ']'
<generic-arg>      := [ <name> ':' ] <expr>
                    | <assoc-type-bound>
```

Generic parameters are used to pass types and value to a generic item/type, allowing an instance to be created.
They can be seen as a function which generates a instance with these arguments.

Generic arguments are passed using an [index]-like syntax.
This is done because it is not possible to index functions, closures, and types.

> _Note_: Within a function, these are interleaved with regular arguments

> _Example_
> ```
> struct Foo[T, N: usize] {}
> 
> foo: Foo[i32, 4] = #todo();
> ```

Generic arguments can be provided with the label of their corresponding parameter.
However, this is not required and are mainly meant to provide more info to the user.

> _Example_
> ```
> struct Foo[Elem, Size: usize] {}
> 
> foo: Foo[Elem: i32, Size: 4] = #todo();
> ```

When a `_` is used, this implies that the generic argument will be inferred from the surrounding context.
If all generic arguments could be provided using a `_`, the arguments may be left out.

> _Example_
> ```
> // Both generic arguments can be inferred from `gen_foo()`
> foo: Foo[_, _] = gen_foo();
> 
> // Generic arguments can be left out when all are deduced
> foo2: Foo = gen_foo();
> ```

## Specialization [↵](#generics)

Specialization allows for different version of a generic to exist.
This allows for compile-time divergence based on the passed generic arguments.

This is done by applying either a more specific bound on a generic, or by defining an explicit type for which the specialization is done

Specialization is only supported in [trait implementations].

> _Example_
> ```
> // base case
> impl[T] A[T] as Foo {
>     // ...
> }
> 
> // Specialization for `T == i32`
> impl A[i32] as Foo {
> 
> }
> 
> // Specialization for `T is Foo`
> impl[T is Foo] A[T] {
>     
> }
> ```

> _Note_: Specialization can be mimicked in generic items/types where regular specialization is not possible, using [`const if` expressions]
> 
> For example:
> ```
> fn foo(const T: type) {
>     if T == i32 {
>         // Do something
>     } else {
>         // Do something else
>     }
> }
> ```

### Resolution [↵](#specialization-)

When specialization exists, and an associated item of it is used, the compiler needs to resolve which specialization to use.
This is decided by finding all possible specialization that match a given set of generic parameters and deciding the most specialized one.
If multiple specializations are equally possible, a collision occurs.

This process acts differently for type and value bounds.

> _Note_: The full resolution logic has not been figured out, this represents only the current way of handling resolutions

#### Type bounds [↵](#resolution-)

Type bound can distinguish between specialization solely based on the provided traits.
If a collision occurs because of a constraint, collision resolution needs to occur.

After collecting all possible specializations, when the trait in type bounds differ, the will be filtered out of all posible candidates.
For any 2 specializations, they are filtered down using the following rules, and filtering happens in this order:

1. if bound by traits `A` and `B` respectively, and `A` is a [supertrait] of `B`, the bound using `A` will be filtered out of the possible specializations.

   > _Example_
   > 
   > Assuming we have the traits
   > ```
   > trait A {}
   > trait B: A {}
   > ```
   > 
   > And the specialization candidates
   > ```
   > impl[T is A] Foo[T] as Bar {}
   > impl[T is B] Foo[T] as Bar {}
   > ```
   > 
   > the specialization using `A` will be removed from the possible specializations, as `A` is a supertrait of `B`
 
2. if bound by traits `A` and `A & B` respectively, the bound using only `A` will be filtered out (both `A` and `B` represent any set of traits)

   > _Example_
   > 
   > Assuming we have the specializations:
   > ```
   > impl[T is A] Foo[T] as Bar {}
   > impl[T is A & B] Foo[T] as Bar {}
   > ```
   > 
   > the specialization using only `A` will be removed from the possible specializations, as `A & B` is a more specific bound than `B`

3. if bound to the same traits `A`, but differ in how they are bound, specifically if one is bound by `?A`, and the other is either bound by `!A` or `A`, the bound using `?A` will be filtered out

   > _Example_
   > 
   > Assuming we have the specializations:
   > ```
   > impl[T is ?A] Foo[T] as Bar {}
   > impl[T is A] Foo[T] as Bar {}
   > ```
   > 
   > the specialization using `?A` will be removed from the possible specializations as `A` is a supertrait of `B`


Combinition between these rules might make resolution seemingly more complex, below are some more examples showing these situations and how they are resolved:

> _Example_
> 
> Assuming we have the specializations:
> ```
> impl[T is A] Foo[T] as Bar {}
> impl[T is ?A & B] Foo[T] as Bar {}
> ```
> 
> The bound using `A` will be filtered out before we reach step 3, resulting in the `?A & B` bound to be kept.
> Although `A` would have been more specific than `A`, by the fact that both `?A` and `B` as bounds, the bound is stronger, as it satifies more traits

> _Example_
> 
> Assuming we have the traits
> ```
> trait A {}
> trait B: A {}
> ```
> 
> And the specialization candidates
> ```
> impl[T is A & C] Foo[T] as Bar {}
> impl[T is B] Foo[T] as Bar {}
> ```
> 
> The bound using `A & C` will be filtered out before we reach step 2, resulting in the bound using `B` being kept.
> Although `A & C` is bounded by 2 traits, the fact that `B` has `A` as a supertrait, it means that this version is more specific

There are however some situation which are not resolvable by these rules

> _Example_
> 
> Assuimg we have the specializations
> ```
> impl[T is A & ?B & ?C] Foo[T] as Bar {}
> impl[T is ?A & ?B & C] Foo[T] as Bar {}
> ```
> 
> This will result in a collision because:
> - both are bound by 3 traits, so neither is more specific in this way
> - since both have 1 positive and 2 optional bounds, neiter is more specific in this way

#### Value bounds [↵](#resolution-)

Value bounds are fairly simple, as they just need to evaluate to a `true` value.
No value bound is inherently more specialized than any other.

If value bounds are the only difference between 2 possible specialization, a collision needs to be resolved.

### Collisions [↵](#specialization-)

A collision occurs when, for a set of specializations, no specialization is more specific than another.
Meaning that 2 or more specializations, which have the narrowest bounds, are valid for a single type.

Since trait implementation must be coherent, it guarantees that any possible collision will be contained to a single library.
Because of this, we know that when compiling a library, we will have all possible specializations, so can require any collisions to be resolved at compilation of that library.

> _Note_: Explicit implementations in other libraries cannot result in a collision with the current library, as they are specific to one of the types defined in that library.
>         And any specializations that are made on it, are fully contained within that library.

> _Example_
> 
> Collision using type bounds, where both trait bounds are independent of each other, meaning that one trait being specialized on is not a base trait of the other.
> ```
> trait Foo[T] {
>     fn foo();
> }
> 
> struct A;
> impl[T is Bar] A as Foo[T] {
>     fn foo() { ... }
> }
> impl[T is Baz] A as Foo[T] {
>     fn foo() { ... }
> }
> 
> struct B;
> impl B as Bar;
> impl B as Baz;
> 
> // variant cannot be resolved here, as the `B` generic arguments is a valid type for either `foo` implementation
> a := A;
> a.(Foo[B].foo)();
> ```

> _Example_
> 
> Collision using value bounds that overlap
> ```
> trait Foo[N: usize] {
>     fn foo();
> }
> 
> struct A;
> impl[N: usize] A as Foo[N] where N > 0 { ... }
> impl[N: usize] A as Foo[N] where N < 2 { ... }
> 
> // variant cannot be resolved here, as the value of `1` satifies the bound for either `foo` implementation.
> a := A;
> a.(Foo[1].foo)();
> ```

### Resolving collisions [↵](#collisions-)

A collision must be resolved to be able to successfully compile a library.

#### Fuzzy bounds [↵](#resolving-collisions-)

Fuzzy bounds use optional bounds to allow 2 independent implementation that might collide, to not collide.
This happens, because an optional bound indicates that while not requiring that trait, if it is implemented, it will make the implementation more specialized for it.

> _Example_
> ```
> impl[T is Bar & ?Baz] A as Foo[T] { ... }
> impl[T is Baz] A as Foo[T] { ... }
> 
> // When calling the function on `a`, the fuzzy `Baz` bound on the first implementation, means that the first specialization will be used, as having both `Bar` and `?Baz` is more specialized than `Baz`
> a.(Foo[B].foo)();
> ```

#### `spec_priority` [↵](#resolving-collisions-)

A `spec_priority` attribute may be added to the specialization.
This attribute takes in an integer value defining the priority of the specialization.
When encountering a collision, the compiler will select the specialization with the highest priority.
The default priority for all specializations is 0.

> _Note_: `spec_priority` can only resolve collision defined within a single library, meaning the priority does not span across diferent libraries

> _Example_
> ```
> @spec_priority(1)
> impl[T is Bar] A as Foo[T] { ... }
> 
> impl[T is Baz] A as Foo[T] { ... }
> 
> 
> // When calling the function on `a`, where `B` implements both `Bar` and `Baz`, the priority of the `Bar` implementation will come in play that that implementation will be used.
> a.(Foo[B].foo)();
> ```

## Restrictions [↵](#generics)

Since certain generic implementations may result in a recursive or infinitally expanding monomorphization, they have a set of restrictions which are meant to prevent this.

### Recursive dependencies [↵](#restrictions-)

Implementations may not have a circular dependency on itself, which may cause a recursive monomorphization.

This means that for any implementation `A` that depends on `B`, `B` may not depend on `A`

_Example_
```
impl[T] A[B[T]] as Foo where B[T] is Foo { .. }
impl[T] B[A[T]] as Foo where A[T] is Foo { .. }
```

### Widening types  [↵](#restrictions-)

Types cannot be widened, as this will result in a runaway monomorphization.

Widening types means that the type is bound by a type which contains the original type itself.

> _Example_
> ```
> impl[T] T where T is ^T {}
> ```
> would result in a runaway monomorphization, as `T` needs to adhere to being itself of type `^T`.
> This means that to be generic over `T`, it must be generic over `^T`, which then requires it to be for `^^T`, and so on.

> _Note_: while the following would cause an issue
> ```
> impl[T] Foo[T] where T is []T {}
> ```
> since T is in its own bound.
> 
> But the following is allowed
> ```
> impl[T] Foo[[]T] {}
> impl[T, U] Foo[T] where T is []U {}
> ```
> The first one explicitly uses `[]T`, and does not put a bound on it.
> And the latter is bound by a different generic value


[call site]:               #usage-site-collisions-q

[`spec_priority`]:         ./attributes/code-generation.md "Todo: correct link"
[index]:                   ./expressions/index-expressions.md
[type check expression]:   ./expressions/type-check-expressions.md
[`const if` expressions]:  ./expressions/if-expressions.md
[function]:                ./items/functions.md
[deduced]:                 ./items/functions.md#deduced-parameters-
[varaidic parameters]:     ./items/functions.md#variadic-parameters-
[trait implementations]:   ./items/implementations.md#trait-impl-
[coherence rules]:         ./items/implementations.md#coherence-
[coherent]:                ./items/implementations.md#coherence-
[supertrait]:              ./items/traits.md#supertraits-
[dynamically sized types]: ./type-system/dynamically-sized-types.md
[builtin type]:            ./type-system/types/builtin-types.md

[`StructuralIdentity`]: ./operators/core-operators.md#comparison-operators- "Todo: link to docs"