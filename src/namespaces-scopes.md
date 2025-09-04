# Namespaces & Scopes

## Namespaces

A namespace is a grouping of _named entity_.
These are located within separate namespaces, depending on the _entity_ they belong to.
This entity is what a namespace derives its name from.

A namespace allows a name to occur, even if it already occurs within another namespace, and prevents them from conflicting.

The following shows the kinds of namespaces that exists, and their associated entities:
- type namespaces: a namespace that maps directly to a type or _named entity_
  - [modules]
  - [structs]
  - [unions]
  - [enums]
  - [bitfields]
  - [traits]
  - [type aliases], including [opaque types]
  - [primitive types]
  - [string slice types]
  - [generic types]
  - [`Self` types]
  - [tool attributes]
  - [meta functions]
  - unnamed types
- value namespaces: a namespace that is not user accessible from outside of the namespace
  - [constructing expressions]
  - local bindings, i.e.
    - [variable declarations]
    - [let bindings]
    - [result if] bindings
    - [`while else`], [`do while else`] bindings
    - [`for`], [`for else`] bindigns
    - [`match`] arms
    - [function parameters]
    - [closure] parameters
  - captured [closure] variables
- value-type namespaces: a namespace that is both a type and a value namespace
  - [function declarations]
  - [constant item declarations]
  - [static item declarations]
- label namespaces: a namespace that maps to labels
  - [function parameter labels]
  - [loop & block labels]

> _Note_: Duplicate names may exists across multiple namespace kinds.
>         For example, both a module and a function named `foo` may coexists within the same scope.

### Named entities without a namespace

In addition, a couple of _named entities_ do not belong to a namespace and can only be accessed using specific operations

#### Fields

Although the items contain fields, i.e. structs, enums, unions, and bitfields, all have their own namespace.
Any fields within these do not, as they are only accessible via one of the following:
- [field access expression]
- [key-path expressions]

These operation only inspect the field names of the specified type being accessed.

#### Use declarations

While [use item] imports name aliases into a given _scope_, the `use` item itself does not introduce a new namespace.
It can only introduce names into the namespace it is located in.

### Meta namespaces

Meta functions and attributes work specially within a namespace, they live in a namespace split up by their type, meaning that there is a separate namespace for meta functions and each kind of meta attribute.
This allows multiple different kinds of meta item to have the same name, but work differently depending on their type.

## Scopes

A _scope_ is a region in code where a _named entity_ may be referenced via its name.
This section defines the scoping rules and behavior, which depend on the kind of entity and where it is declared.

### Item scopes

When a item is declared within another item or in a statement, it is accessible from the start to the end of the scope the item is in.
It can be referred to by using a path relative to the scope the _entity_ is in.

Defining 2 _named entities_ with the name scope (and same conflicting labels for functions/methods) will result in an error, as these belong to the same _namespace_.

> _Note_: Glob imports behave differently, as defined [here](./items/use.md#glob-imports-).

Items introduced within a scope may overwrite a similarly named one from a [prelude].

#### Associated item scopes

[Associated items] are not scoped and can only be refered to relative to the type or trait they are asociated with.
Methods can be additionally be accessed using its path in a [call expression].

### Pattern binding scopes

The scope of a pattern binding depends on where it is used.
- [variable declarations]: from just after the binding, until the end of the block where it is declared
- [function parameter] bindings: within the body of the function
- [closure] parameter bindings: within the body of the closure
- [`for`] bindings: within the body of the loop
- [let bindings] range: from just after the binding, until the end of the body of the if/loop it is defined in.
- [`match`] arms: in any [guard patterns] after it, and in the arm's body

Local variable scopes do not extend into items

#### Pattern binding shadowing

Pattern bindings are allowed to shadow any name in scope with the folloiwng exception, which will result in an error.
- [const items]
- [static items]
- [value generics]
- names used in [struct patterns]

### Implicit variable scopes

The scope of an implicit variables are limited to the scopes of:
- property [accessor] or [observer] bodies
- [get-only properties]
- [closure] bodies

## Generic parameter scopes

Generic parametrs are declared by [generic parameters] and are visible throughout the item where they are declared.

In addition, they are also available within the all other parameters, regardless of in which order they are.
Meaning that the following will work just fine:
```
struct Foo[T is Trait(N), N: usize] {}
```

Similarly, this also works for any [deduced parameters] or constant [function parameters], as they are interpreted as generic parameters.
Meaning the following will also work:
```
fn foo [T is Trait(N)](const N: usize) {}
```

All [generic parameters] are also available without the [`where` clause].
```
struct Foo[T] where T is Trait {}
```

Any item declared within the scope also has access to the [generic parameters].
```
fn foo[T](val: T) {
    fn bar(val: T) { // `T` is defined by `foo`

    }
}
```

### Generic parameter shadowing

Generic parameters cannot be shadowed, unless it is done inside of a function defined within another function.
Meaning that this is valid:
```
fn foo[T](val: T, const N: usize) {
    fn bar(const T: type) {}
    fn baz(const N: usize) {}
    fn quux(const T: usize) {}
}
```
but this is not
```
struct Foo[T, N: usize] {
    fn bar(const T: type) {} // error: 'T' is already in use
    fn baz(const N: usize) {} // error: 'N' is already in use
    fn quux(const T: usize) {} // error: 'T' is already in use
}
```

## Label scopes

[Loop and block labels] are avaiable from the point where they are declared, to the end of the loop or block they are associated with.
It also does not extend into any other item or [closure] defined within the loop, nor the iterator expression within a [`for`] loop.


may only shadow another label within the same scope, but not in any inner scope.

```
:a: for n in 0..2 {
    if n % 2 == 0 {
        break :a;
    }
    fn inner() {
        // Using :a here will result in an error
        // break :a;
    }
}

// The label is in scope for the entire duration of the `while` expression
// The label `:a` is also shadowed here
:a: while break :a {} // will not run
:a: while let _ = break :a {} // will not run

:a: for outer in 1..4 {
    // This will break the outer loop, skipping the inner loop and stopping the outer loop,
    // as :a within the iterator expression still refer to the outer one, since the inner `:a` is not in scope yet
    :a: for innter in { break :a; 0..1 } {
        b = 1; // does not run
    }
    b = 2 // Does not run eitehr
}
```

## Prelude scopes

Preludes may bring _entities_ into every module.
These are not members of the modules, but can be refered to like how any [use items] bring entities into scope.

Prelude names may be shadowed by explicitly imported names, or by declaration within the scope.

Preludes are layered, so one may shadow import of another, if both contain the same name.
The order that preludes shadow each other is the following:

- tool preludes
- custom preludes (according to the order given to the compiler)
- `std` prelude (if `std` is used)
- `core` prelude

## Self scopes

Although `Self` has a special meaning, it interacts with name resolution in a similar way to normal names.

The implicit `Self` type in the definition of [structs], [enums], [unions], [bitfields], [traits] and [implementations] is handled like a generic parameter.

The `Self` type is also available within any associated item.

```
struct Recursive {
    f1: ?Box(Self)
}

struct SelfGeneric[T: To(Self)](T);

struct ImplExample() {
    fn example() -> Self { // Self type
        Self() // Self value constructor
    }
}
```



[`Self` types]:               #namespaces "Placeholder"
[tool attributes]:            ./attributes/#tool-attributes-
[call expression]:            ./expressions/call-expressions.md
[constructing expressions]:   ./expressions/constructing-expressions.md
[field access expression]:    ./expressions/field-access-expressions.md
[let bindings]:               ./expressions/if-expressions.md#let-bindings-
[result if]:                  ./expressions/if-expressions.md#result-if-expressions-
[`do while else`]:            ./expressions/loop-expressions.md#do-while-else-
[`for`]:                      ./expressions/loop-expressions.md#for-expression-
[`for else`]:                 ./expressions/loop-expressions.md#for-else-
[loop and block labels]:      ./expressions/loop-expressions.md#loop-labels-
[Loop and block labels]:      ./expressions/loop-expressions.md#loop-labels-
[`while else`]:               ./expressions/loop-expressions.md#while-else-
[`match`]:                    ./expressions/match-expressions.md
[key-path expressions]:       ./expressions/key-path-expressions.md
[generic types]:              ./generics.md
[value generics]:             ./generics.md
[generic parameters]:         ./generics.md#generic-parameters-
[`where` clause]:             ./generics.md#where-clauses-
[const items]:                ./items/consts.md
[constant item declarations]: ./items/consts.md
[function declarations]:      ./items/functions.md
[function parameter labels]:  ./items/functions.md#labels-
[deduced parameters]:         ./items/functions.md#deduced-parameters-
[function parameters]:        ./items/functions.md#parameters-
[function parameter]:         ./items/functions.md#parameters-
[modules]:                    ./items/modules.md
[implementaions]:             ./items/implementations.md
[Associated items]:           ./items/implementations.md#associated-items-
[static item declarations]:   ./items/statics.md
[static items]:               ./items/statics.md
[traits]:                     ./items/traits.md
[type aliases]:               ./items/type-aliases.md
[use items]:                  ./items/use.md
[meta functions]:             ./metaprogramming.md
[guard patterns]:             ./patterns/guard-patterns.md
[struct patterns]:            ./patterns/struct-patterns.md
[prelude]:                    ./preludes.md
[tool preludes]:              ./preludes.md "Todo: fix link"
[custom preludes]:            ./preludes.md#263-custom-preludes-
[`core` prelude]:             ./preludes.md#263-core-prelude-
[`std` prelude]:              ./preludes.md#263-core-prelude-
[variable declarations]:      ./statements/variable-declarations.md
[closure]:                    ./type-system/types/closure-types.md
[bitfields]:                  ./type-system/types/bitfield-types.md
[enums]:                      ./type-system/types/enum-types.md
[primitive types]:            ./type-system/types/primitive-types.md
[string slice types]:         ./type-system/types/string-slice-type.md
[structs]:                    ./type-system/types/struct-types.md
[opaque types]:               ./type-system/types/opaque-types.md
[unions]:                     ./type-system/types/union-types.md
