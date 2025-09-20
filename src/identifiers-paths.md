# Identifiers & Paths

Names, identifiers, and paths are used to refer to things like:
- types
- items
- generic paramters
- variable bindings
- loop labels
- fields
- attributes
- etc.

## Identifiers [↵](#identifiers--paths)

```
<iden-name>     := <name> | <path-disambig>
<ext-iden-name> := <ext-iden-name> | <iden-name>
<iden>          := <iden-name> [ <generic-args> ]
<ext-iden>      := <ext-iden-name> [ <generic-args> ]
```

An identifier is a sub-segment of a path, which consists out of a name and optional generic arguments.
Identifiers refer to a single element in a path which can be uniquely identified by its name and generics.

When the identifier refers to a generic symbol, the generic arguments are passed, this looks similarly to a [function call], but with some slight differences, as defined by [].

> _Note_: An idenfier may also have an [extended name], depending on where it is located.

### Trait disambiguation [↵](#identifiers-)

```
<path-disambig> := '(' <trait-path> '.' <ext-name> ')'
```

Sometimes an identifier can not be resolved directly to a trait's item, this can be caused by the item either not being from an `extend` trait impl, or when at least 2 `extend` trait impl items exists that have the same name, but no explicit (non-trait) item exists on the previous item in the path.
This can be resolved by explicitly prepending a path to the implemented trait, within `()`, that has the desired item to be accessed.
The trait path may not end in a function-style trait path end.

## Paths [↵](#identifiers--paths)

A path is a sequence of one or more identifiers, logically separated by a `.`, with an optional path start and/or a special ending.
If a path consists out of only one segment, and refers to either an item or variable in the local scope.
If a path has multiple parameters, it refers to an (associated) item or field.

Two examples are:
```
x;
x.y.z;
```

### Path start [↵](#paths-)
```
<simple-path-start>       := <path-super-start> | <path-meta-lib-start> | <path-meta-package-start>
<path-super-start>        := 'super' '.' { 'super' . }*
<path-mod-rel-start>      := 'mod' '.'
<path-meta-lib-start>     := '$' 'lib'

<path-start>              := <simple-path-start> | <path-type-start> | <path-self-type-start> | <path-infer-start> | <path-lib-ref-start>
<path-type-start>         := '(:' <type> ':)' '.'
<path-self-type-start>    := 'Self' '.'
<path-infer-start>        := '.'
<path-lib-rel-start>      := ':.'
```

Path start are special syntaxes that may be the first element of a path.
Although they differ between simple paths and regular paths, they are both only allowed at the start of their respective path.

Both path types have some common path starts:
- `super.`
- `mod.`
- `$lib.`

In addition, regular paths have additional path starts:
- `<type>.`
- `Self.`
- `.`
- `:.`

#### `super.` [↵](#path-start-)

The `super.` path start indicates a path that is relative to the parent scope of the current path.
```
mod a {
    fn foo();
}
mod b {
    fn foo() {
        super.a.foo(); // calls a's foo function
    }
}
```

Super may be repeated multiple times after each other.
Each step will go the the parent scope of the scope that the preceeding element of the path start points to.
```
mod a {
    fn foo() {}

    mod b {
        fn foo() {}
        mod c {
            fn foo() {
                super.foo(); // calls b's foo
                super.super.foo(); // calls a's foo
            }
        }
    }
}
```

#### `mod.` [↵](#path-start-)

The `mod.` path start indicates a path that is realtive to the current module.

```
mod a {
    fn foo()

    struct Bar {
        fn baz() {
            mod.foo(); // calls a's foo
        }
    }
}
```

#### `$lib` [↵](#path-start-)

`$lib` is a special meta-variable that can be used within [meta blocks] (or any metafunction it maps to) to generate a path that is relative to the library that defines the [meta function].
It can only be used inside of the any supportred meta function.

```
#{
    $lib.foo(); // Call foo from the library where this is located in
}
```

When used in a [use item], it is equivalent to: `$package:$lib`.

#### `<...>.` [↵](#path-start-)

The `<type>.` path start indicates a path that is relative to a specific type.
This syntax allows unnamed types to be used as the start of a path where they would normally not be able to be used.

```
<^T>.foo(); // Calls `^T`'s foo
```

#### `Self.` [↵](#path-start-)

The `Self.` path start indicates that he path is relative to the [`Self` type].

```
trait T {
    type Item;
    const C: i32;

    // `Self` will be the type that implements `T`
    fn new() -> Self;
    // `Self.Item` will be the type that is defined by type alias in the implementation.
    fn(&self) f() -> Self.Item;
}

struct S {
    impl as T {
        type Item = i32;
        const C: i32 = 42;

        fn new() -> Self { // `Self` corresponds to `S`
            S
        }

        fn(&self) f() -> Self.Item { // `Self.Item` refers to `S.Item`, resulting in a type of `i32`
            Self.C // `Self.C` refers to `S.C`, resulting in a value of `42`
        }
    }
}

// `Self` is also in scope for generics, and thus can be used within the generic parameters
trait Foo<Rhs = Self> {
    // ...
}
```

#### `.` [↵](#path-start-)

The `.` path start indicates that the path is interpreted from the surrouding context

It can be used to refer to the following:
- [enum] variants
- [binddings] or [bounds] to [generic parameters]
- 
> _Todo_: Are enum variants really the only thing that can be referenced here?

```
enum Foo {
    Bar
};

fn foo() {
    let f: Foo = .Bar;
}

fn bar[T is Trait(.Output = i32)](x: T) {}
```


#### `:.` [↵](#path-start-)

The `:.` path start indicates that the path is relative to the library's [main module].

```
fn foo() {}

mod a {
    mod b {
        fn foo() {
            :.foo(); // Calls the main scope's foo
        }
    }
}
```

### Simple paths [↵](#paths-)

```
<simple-path>             := [<simple-path-start>] <simple-path-no-start>
<simple-path-no-start>    := <simple-path-segment> { '.' <ext-simple-path-segment> }*
<simple-path-segment>     := <name>
<ext-simple-path-segment> := <name> | <ext-name>
```

Simple path are used for [visitility], [attributes], [metafunctions], and [use items].

When used within a [visibility] or [use item], a path may start with an additional path start.

### Regular paths [↵](#paths-)
```
<path> := [ <path-start> ] <iden> { '.' <iden> }*
```

Paths are used in expression and types to refer to a given item.
They exists out of an optional path start, followed by a sequence of identifiers.

When a path is used in an expression, they are essentially a sequence of field accesses and method calls.
The only part of the path that will be uniquely identified as a path is the path start and the first identifier (without generics).

### Trait paths [↵](#paths-)

```
<trait-path> := [ <path-start> ] <type-path-segment> { '.' <type-path-segment> }* [ '.' <trait-path-fn> ]
              |  <trait-path-fn>
<trait-path-fn> := <name> '(' <fn-type-params> ')' [ '->' <type-no-bounds> ]
```

Trait paths are a special variation of paths that are used in any location a trait is explicitly expected.
A trait path may end in a special function end, the usecase for is limited to the for function call related traits, allowing the parameters and return type for these to be specified.

> _Todo_: are the function-style endings really needed (as they just seem like special cases for a couple of types), and can we change it into something else?

## Canonical paths [↵](#identifiers--paths)

A canonical path represents the entire path to an entity, indicating where it is defined.
The path and each sub-path either start relative to the library, or with a [type start].
Any entity which has a canonical path can (possibly) be refered to from outside the scope they are defined in.

Both [implementations] and [use items] do not have a canonical path, but the _entities_ or _type_ they refer to have canonical paths.
In addition, some entities do not have a canonical path themselves, these are:
- field in [composite types]
- items defined inside of:
  - [functions]
  - [block expressions]
  - items without a canonical path
- associated items of an item without a canonical path

When an entity is part of a trait implementation of a type, the trait ambiguation is included.

Canonical paths have no meaning outside of the given library, as it is defined to be relative to the current library.
The actual _symbol_'s path is a variant on this, which includes the package and library to which each sub-path is relative to.

Inside of the library, the following items have the following canonical paths:
```
mod c { // :.c
    pub struct Struct; // :.c.Struct

    pub trait Trait { // :.c.Trait
        fn f(&self); // :.c.Trait.f
    }

    impl Struct as Trait {
        fn f(&self) {} // :.c.Struct.(:.c.Trait.f)
    }

    impl Struct {
        fn g(&self) {} // :.c.Struct.g
    }
}

mod no { // :.no
    fn canonical() { // :.no.canonical
        struct OtherStruct; // n/a

        pub trait OtherTrait { // n/a
        fn f(&self); // n/a
        }

        impl OtherStruct as OtherTrait {
            fn f(&self) {} // n/a
        }

        impl OtherStruct {
            fn g(&self) {} // n/a
        }
    }
}
```




[type start]:         #-
[attributes]:         ./attributes.md
[block expressions]:  ./expressions/block-expressions.md
[function call]:      ./expressions/call-expressions.md
[generic arguments]:  ./generics.md#generic-arguments-
[generic parameters]: ./generics.md#generic-parameters-
[enum]:               ./items/enums.md
[functions]:          ./items/functions.md
[implementations]:    ./items/implementations.md
[use item]:           ./items/use.md
[use items]:          ./items/use.md
[extended name]:      ./lexical-structure/names.md#extended-names-
[metafunctions]:      ./metaprogramming.md
[meta function]:      ./metaprogramming.md
[meta block]:         ./metaprogramming/meta-utilities.md#meta-blocks-
[composite types]:    ./type-system/types/composite-types.md
[type inferred]:      ./type-system/types/inferred-types.md
[`Self` type]:        ./type-system/types/self-type.md
[visibility]:         ./visibility.md
[main module]:        ./package-structure.md#main-module-
