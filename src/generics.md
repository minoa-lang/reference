# Generics

Generics allows for code reuse by writing generalized versions of items and types that can take in compile time constants.
Generics are compile-time arguments the compiler can use for this.

While types themselves are values, there are some slight differences between type and (other) value generics, as specified below:
- type generics have an implicit bound to `Sized`, and need an explicit optional or negative bound to relax this implicit bound.
- value generics require to be either a builtin type, or a type adhering to the `StructuralIdentity` trait

The act of creating a version of a generic item, is called instanciation or monomorphization.

Type parameters also allow bounds to be specified at their declaration site, this is syntactic sugar for the same bound in a where clause.

## Generic parameters [↵](#generics)
```
<generic-params>        := '[' <generic-param> { ',' <generic-param> }* [ ',' ] ']'
<generic-param>         := <name> [ <generic-param-type> ] [ '=' <expr> ]
<generic-param-type>    := ':' <type>
                         | 'is' <trait-bounds>
```

Generic parameter allow the adding of generics to items.

If no explicit type is provided for a generic parameter, `type` will be assumed

### Optional parameters [↵](#generic-parameters-)

Generic parameters may be provided with a default value for when no explicit value is given when instantiating a generic value, these will then be optional generic parameters.
Similarly to optional function parameters, they must appear after all non-optional (also known as required) generic parameters.

Unlike in functions, since generic parameters don't have label, generic arguments must come in the same order as its corresponding parameters.

## Generics in functions [↵](#generics)

Generics within a function is handled by the compile-time arguments passed into a function.

These arguments can be used just like any generic parameter, but can also be used to precompute values withing code.

When a generic function is passed into a place that expects a closure, the function can 'lowered' to bake in the generic arguments, using an `...` (elipsis) expression.

For example, if we have the following generic function
```
fn foo(:_ const T: type, :_ a: T) { ... }
```

and some code which can take in a function
```
fn bar(foo: fn(i32)) { .. }
// or
fn bar(const T: type, foo: T) where T is Fn(i32) { ... }
```

Then `foo` can be converted to a matching function
```
bar(foo: foo(i32, ...))
// or
bar(foo: foo(_, ...))
```

## Parameter packs [↵](#generics)
```
<parameter-pack>  := <param-pack-name> '...' [ ':' <param-pack-desc> ] [ '=' <expr> { ',' <expr> } [ ',' ] ]
<param-pack-name> := <name>
                   | '(' <name> { ',' <name> } [ ',' ] ')'
<param-pack-desc> := <type>
                   | 'is' <generic-bound>
```

A parameter pack is an array of 0 or more instances of a generic parameter set.
Parameter packs therefore allow for an unknown amount of generic arguments to be passed to an item.
Parameter packs are only allowed as the last parameters withing a generic parameter declaration.

A parameter set is a collection of names/typed provided to the generic item.
A set may consist out of either a single names element, or a destructured group of multiple sub-elements.

When providing values, for parameter set, all elements need to be passed, meaning that the total amount of arguments passed as generic arguments needs to be an integer multiple of the number of names defined within the variadic parameter's definition.


Like any other generic parameters, the parameter pack can be given a name.
But unline other parameters, if a parameter set contains multiple element, they can be destructured into muliple sub-packs.
The number of sub-packs must be equal to the number of elements within a parameter pack's description.

If no parameter pack description is provided, all sub-packs will be defaulted to `type`.
If a sub-pack is specified as `is ...`, then this will also be of type `type`, but with the constraints specified applied to it.
The description also specifies the group size, i.e. how many elements are in each occurance of the pack.

A parameter pack may also be optional, by provided a default value, the number of default values must consists of an integer mulitple of the description's group size, and must be compatible with the type in the description.

> _Note_: This is related to [variadic parameters] in functions

> _Todo_: Figure out ergonimics, i.e. number of params, looping over them, etc. A set of metafunction will be used for this, as they are a SOA of values

## Where clauses [↵](#generics)
```
<where-clause> := 'where' <expr> { ',' <expr> }
<generic-bound> := <expr>
                 |
```

A where clause is a collection of bounds that the generic parameters need to adhere to.
This contains all bounds generic arguments must adhere to when creating an instance of the item.

Bounds are comma separated list of boolean expression determining if the given value adheres to it

Since type are values, they can be handled using any expression, similar to how values can be bound.

## Trait bounds [↵](#generics)
```
<trait-bounds>       := <path> { '&' <path> }
<trait-bound>        := <path> [ '(' <trait-assoc-bounds> ')' ]
                      | '?' <path>
                      | '!' <path>
<trait-assoc-bounds> := '.' <name> 'is' <type-bound>
                      | <return-type-trait-bound>
```

A trait bound is used to check if a given type adheres to a set of trait.

Trait can be checked in 3 different ways:
- Positive bounds: this requires a type to implement the given bound
- Optional bounds: this allows a type to possibly implement a type, this is required if any specialization on a type based on whether this trait is implemented or not.
  In case of `Sized`, relaxes the default bound and allows a type to have a dynamic size.
- Negative bounds: this requires that a type does __not__ implement a given type.

## Return type bounds [↵](#generics)
```
<return-type-bound>       := <path> '(' '..' ')' 'is' <trait-bounds>
<return-type-trait-bound> := '.' <name> '(' '..' ')' 'is' <trait-bounds>
```

Return type bound allow bound to be applied on the return types of a function or method, that is associated with one of the generic types.

## Constraints [↵](#generics)
```
<constraint-item>   := { <attribute>* } [ <vis> ] 'constraint' <name> [ <generic-params> ] '{' <constraint-members> { ',' <constraint-member> } [ ',' ] '}'
<constraint-member> := <trait-item>
                     | <expr>
```

A constraint is similar to a trait, except that it does not need to be explicitly implemented.
In addition, a constraint may also contain generic bounds in addition to the body.

Constraint items may only act on directly associated items, meaning associated items directly implemented (i.e. without an trait), and [extended trait items]().

> _Todo_: Link to extended trait item implementations

### Constraint functions & methods [↵](#constraints-)
```
<constraint-fn>        := [ 'unsafe' ] [ 'const' ] 'fn' <name> [ <generic-params> ] '(' <constraint-fn-params> ')' [ '->' <fn-return> ] [ <where-clause> ] ';'
<constraint-fn-params> := <constraint-fn-param> { ',' <constraint-fn-param> } [ ',' ]
<constraint-fn-param>  := <name> ':' <type>

<constraint-method>    := [ 'unsafe' ] [ 'const' ] 'fn' '(' <receiver> ')' <name> '(' <constraint-fn-params> ')' [ '->' <fn-return> ] [ <where-clause> ] ';'
```

Constraint functions and methods are very similar to [trait functions & methods].

But they use a simplified set of parameters and may not contain:
- attributes
- a body
They require that any type has a function or method matching the signature of the constraint function or method.

A constraint function's parameter exist out of 2 things:
- a name corresponding to the label of that variable (may include keywords), and
- the type of the argument

If a function contains an parameter with `N` names, it needs to be expressed by `N` parameters in the constraint function.

### Constraint type aliases [↵](#126-constraints-)
```
<constraint-type-alias> := 'type' <name> [ <generic-params> ] [ <where-clause> ] ';'
```

Constraint type aliases are very similar to [trait type aliases], but cannot have a default value.
They require that any type has a type alias with a matching name and type.

### Constraint constants [↵](#126-constraints-)
```
<constraint-const> := 'const' <name> ':' <type> ';'
```

Constraint constants are very similar to [trait constants], but cannot have a default value.
They require that any type has a constant with a matching name and type.

### Contraint properties [↵](#126-constraints-)
```
<constraint-prop>        := 'property' ( <name> | <int-literal> ) ':' <type> '{' <constraint-prop-member> '}'
<constraint-prop-member> := [ [ 'mut' ] 'ref' ] 'get' ';'
                          | 'set' ';'
```

Constaint properties are very similar to [trait properties], but cannot have default implementations.
The requires that any type has either a property of field matching the name and type.

When a property has an integer literal instead of a name, this is associated with a tuple index field withing the constained type.

### Constraint disambiguation aliases [↵](#constraints-)
```
<constrait-disambig-alias> := 'alias' <name> '=' <trait-path> '.' <name> ';'
```

A constraint disambiguation alias is an alternate name given to an item in a trait, which can be used as a disambiguation in a path.

For example: `alias do_foo = Foo.do_something` allows the following 2 call to essentially be the same thing:
```
value.(Foo.do_something)();
value.do_foo();
```

## Generic arguments [↵](#generics)
```
<generic-args> := '(' <expr> { ',' <expr> }* [ ',' ] ')'
```

Generic arguments are the same as normal constant function arguments, except that they can also be used on non-function items.

> _Note_: Within functions, these are interleaved with runtime arguments

## specialization [↵](#generics)

Specialization allows a different generic item to be generated depending on the arguments passed.
These are split up in 2 kinds:

### Internal specialization [↵](#specialization-)

Internal specialization can only happen in a function, and is done using branches on a commpile-time conditional within a function.

For example:
```
fn foo(const T: type) {
    if T == i32 {
        // Do something
    } else {
        // Do something else
    }
}
```

### External specialization [↵](#specialization-)

External specialziation is a mechanism that allows specialization of trait implementation on types.
This is done by passing an explicit expression that does not include on of the `impl`'s generic parameters.

> _Note_: Specialization may only occur on trait implementations, any other specialization needs to be done using internal specialization

For example:
```
// Base case
impl(T) A(T) as Foo {
    ...
}

// Specialization
impl A(i32) as Foo {

}
```

#### Collisions [↵](#external-specialization-)

A collision occurs when neither implementation is more specific then another, meaning that 2 or more differently bound specializations may be valid for a single type.

Since we have the orphin rule for traits, we can be certain that any possible collision will be limited to within the current library.

Collisions can happen on trait bounds, such as independent type bounds where 2 or more bounds that aren't each other base traits, for example
```
trait Foo(T) {
    fn foo();
}

struct A;
impl(T is Bar) A as Foo(T) {
    fn foo() { ... }
}
impl(T is Baz) A as Foo(T) {
    fn foo() { ... }
}

struct B;
impl B as Bar;
impl B as Baz;

// Variant cannot be resolved here, the `B` generic argument is a valid type for either `foo` implementations
a := A;
a.(Foo(B).foo)();
```

Or they can happen on any expression bound:
```
trait Foo(N: usize) {
    fn foo();
}

struct A;
impl(N:usize) A as Foo(N) where N > 0 { ... }
impl(N:usize) A as Foo(N) where N < 2 { ... }

a := A;
a.(Foo(B).foo)();
```

#### Resolving Collisions [↵](#external-specialization-)

Collision can be resolved usign the `spec_priority` attribute, which allows an integer value to be used to decide what specialization should be preferred during specialization.
A higher value means that the specialization will be prefered over any other specialization with a lower value. By default, the priority is set to 0.

The above example using trait bounds can be fixed in the following way
```
...

// Now this will be picked over the implementation below it
@spec_priority(1)
impl(T is Bar) A as Foo(T) {
    fn foo() { ... }
}

impl(T is Baz) A as Foo(T) {
    fn foo() { ... }
}

...

```
### Restrictions [↵](#generics)

As specialization may be implement on top of any type, this may lead to the possibility of having either recursive or infinitly expanding monomorphization.
To prevent this, any specialization has 2 limits placed on in:
- Specializations may not be recursive, meaning that a specialization may not rely on the exact same specialization being resolved
- Specializations may not widen a type

#### Recursive dependencies

Recursive dependencies are caused if a specialization for `A` requires a specialization of `B`, but `B` also requires one for `A`.

For example:
```
impl(T) A(B(T)) as Foo where B(T) is Foo {}
impl(T) B(A(T)) as Foo where A(T) is Foo {} 
```

#### Widening types

Types are widened, when a type in specialization is constrained by a type, which itself contains the type it is contraining.

For example
```
impl(T) T where T is ^T {

}
```

In this case, we are specializating on `T` by requiring it to adhere to being a version of `^T`.
The issue is that `^T` itself contains `T`, which would cause a run-away type check, as specializing for `T`, requires a specialization for `^T` to exists, which in turn requires one for `^^T`, etc.

Below are some more examples which would cause this issue:
```
impl(T) DynArr<T> where T is DynArr<T> {}
impl(T) DynArr<T> where T is []T {}
```


[trait constants]:           ./items/consts.md#trait-constant-
[trait functions & methods]: ./items/functions.md#trait-functions--methods-
[variadic parameters]:       ./items/functions.md#variadic-parameters-
[trait properties]:          ./items/properties.md#trait-properties-
[trait type aliases]:        ./items/type-aliases.md