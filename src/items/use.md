# Use items

```
<use-item>       := 'use' <use-root> [ ( '.' <use-tree> ) | <use-tree-tail> ] ';'
                  | <use-tree> ';'
<use-root>       := [ <package-name> ] ':' [ <module-name> ]
                  | 'super'
<package-name>   := [ <name> '.' ] <name>
<module-name>    := <name>
<use-tree-tail>  := <use-glob>
                  | <use-grouping>
                  | <use-alias>
<use-tree>       := <use-path> <use-tree-tail>
<use-tree-inner> := <use-tree>
                  | 'self' [ 'as' <name> ]
<use-path>       := <name> { '.' <name> }*
```

A `use` declaration creates a local binding associated to a item path.
They can be used to:
- introduce a library's root module into the scope
- introduce a specific item into the scope
- Shorten the path required to reference an item

These declarations may appear in modules and blocks.

> _Note_: To import the contents of a file, use the `#embed()` meta-function

> _Todo_: Could be interesting to support directly including C headers, i.e. `use "header.h";` and `use "header.h" as ffi;`

## Use visibility [↵](#use-declarations-)

Like other items, `use` declarations are private to the containing module by default.
But it can also have its visibility declared, while for most items, this is explained in the [visibility sectoin](../visibility.md), visibility attributes work slightly differently on `use` declarations.
`use` declaration can be used to re-export symbols to a different target definition with a different visibility and/or name.
For example, a symbol with a more restricted visibility like 'private' in one module, can be changed to a `pub` symbol in another module.
If the resulting sequence of re-exports form a cycle or cannot be resolved, a compiler error will be emitted.

An example of redirection:
```
mod quux {
    use :.foo.{bar, baz};
    pub mod foo {
        pub fn bar() {}
        pub fn baz() {}
    }
}

fn main {
    quux.bar();
    quux.baz();
}
```

## Use root [↵](#use-declarations-)

Each use path starts with a root which indicates relative to what the path is located.

A use declaration may look for the path in within one of the following:
- a specified library
- the current module
- the parent module

To access a a specified library, the declaration start by indicating the path to the library.
This is called the root name and is shown as `package:library`, these do not need to explicitly be written down in the following usecases:
- the package can be left out if the path refers to the current package
- the library can be left out in 2 cases:
    - If there is no explicit package (i.e. the current package), it will refer to the current library or binary
    - If there is an explicit package, it will refer to the library within that package with that same name

An example of this can be seen in the below table for the following project structure:
```
A (package)
- Cur (lib)
- A (lib)
- C (lib)
B (package)
- B (lib)
- D (lib)
```

And with the current library being `Cur`, the path will point to the following packages and libraries

Use root | Package | Library
---------|---------|---------
`:`      | `A`     | `Cur`
`:C`     | `A`     | `C`
`A:`     | `A`     | `A`
`B:`     | `B`     | `B`
`B:D`    | `B`     | `D`

The package name may contain 2 parts: an optional group name, and the actual name of the package.
For more info can be found [here](../package-structure.md).

For example, the Minoa std packages' experimental library is accessed as `Minoa.std:experimental`, where:
- `Minoa` is the group that the library belongs to
- `std` is the package under which the library is distributed
- `experimental` is the actual library.

The `use` root can be omitted for any value relative to the current module, including at most 1 level up using the `super` keyword.

## Use path [↵](#use-declarations-)

A use path is the main path to a symbol which appears after after a root, or as the first element for a sub-path within a [use grouping](#use-grouping-).
A use path exists out of a list of names, which can each refer to a entity within the previous segments namespace.

Items each segments may refer to can be one of the following:
- Nameable [items](../items.md)
- [Enum variants](../type-system/types/enum-types.md)
- Builtin types ([primitive types](../type-system/types/primitive-types.md) & [string slices](../type-system/types/string-slice-type.md))
- [Attributes](../attributes.md)
- [Metaprogramming items](../metaprogramming.md)

The cannot refer to any of the following:
- associated items
- generic parameters
- local variables
- tool attributes
- items declared inside of [functions](../items/functions.md)

If no path tail is added, the path will create a binding to the imported entities, with the exception of [`self`](#self-imports-).

A use-path may end in a tail, which is one of the following:
- [glob imports](#glob-imports-)
- [use groupings](#use-groupings-)
- [use aliases](#use-aliases-)

## Glob imports [↵](#use-declarations-)
```
<use-glob> := '.' '*'
```

A glob import is used to import all entities that are located in the namespace pointed to by the preceeding path.

Items and named imports are allowed to shadow names from glob impoorts within the same namespace.
Meaning that if a name is already defined, while also being imported via a glob, the name that is already defined takes precedences over the glob imported entity.

## Use groupings [↵](#use-declarations-)
```
<use-grouping>   := '.' '{' <use-tree-inner> { ',' <use-tree-inner> }* [ ',' ] '}'
<use-tree-inner> := <use-tree>
                  | 'self' [ <use-alias> ]
```

A use grouping allows multiple entities to be imported from the location pointed to by the preceeding path.
Use grouping can be nested within each other.

An emty use-grouping is allowed, but this will not import anything, although the preceeding path will still be checked to be valid.

#### `self` imports [↵](#use-grouping-)

A `self` import is a special import that is only allowed inside of an import grouping.
It allows the entity at the sub-path before the grouping to be imported in addition to any other entities in the path.

If the `self` import is the only element in an import grouping, the path will act the same as it would have been without the use grouping.

## Use aliases [↵](#use-declarations-)
```
<use-alias> := 'as' <name>
```

A use alias allows an imported entity to be bound to another name.

#### Underscore imports [↵](#use-aliases-)

Items can be imported without binding to a name by using an `_` (underscore) alias.
This is particularly useful to import a trait so that its methods may be used without importing the trait symbol, for example if the trait's symbol may conflict with another symbol.

## Import ambiguity [↵](#use-declarations-)

Certain situation might cause multiple `use`s to result in an ambiguity when trying to resolve a symbol, and the symbols do not resolve to the same entity.

Glob imports are allowed to import conflicting names, as long as the name is not used.
If the the conflicting names were to refer to the same entity, the name may still be used.
The visibility of such an entity will then become the greatest visibility between the imports.

For example, if both libraries were to contain `foo`, the following would lead to an ambiguity:
```
use :lib0.*;
use :lib1.*;

fn main() {
    foo(); // ambiguous symbol, specify `lib0.foo` or `lib1.foo`
}
```
it is then possible to explicitly specify which lib `foo` should come from
```
use :lib0.*;
use :lib1.{*, foo}; // Still importing everything, but foo is named now

fn main() {
    foo(); // This is now `lib1.foo`
}
```
