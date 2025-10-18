# Use items

```
<use-item>       := 'use' <use-root> [ <use-path> ] ';' 
                  | `use` <use-path> ';'
```

A `use` item creates onr or more local name bindings associated with a given path.
They can be used to:
- introduce a library's root module into the scope
- introduce a specific item into the scope
- Shorten the path required to reference an item

These declarations may appear in modules and blocks.

> _Note_: To import the contents of a file, use the [`#embed()`] meta-function

## Use visibility [↵](#use-declarations-)

Like other items, `use` declarations are private to the containing module by default.
It can also have its visibility declared explicitly, while for most items this is explained in the [visibility section], visibility attributes work slightly differently on `use` declarations.
`use` declaration can be used to re-export symbols to a different target definition, with a different visibility and/or name.
For example, a symbol with a more restricted visibility like 'private' in one module, can be changed to a `pub` symbol in another module.
If the resulting sequence of re-exports form a cycle or cannot be resolved, a compiler error will be emitted.

If a re-export were to result in cycle, or if it introduce unambiguity, this will result in an error.

> _Example_: redirection of a named item:
> ```
> mod quux {
>     // This now makes both `bar` and `baz` visible from this scope
>     use :.foo.{bar, baz};
>     pub mod foo {
>         pub fn bar() {}
>         pub fn baz() {}
>     }
> }
> 
> fn main {
>     // Now use these functions directly from the `quux` scope
>     quux.bar();
>     quux.baz();
> }
> ```

## Use root [↵](#use-declarations-)
```
<use-root> := <use-lib-root> '.'
            | <simple-path-start>
            | <lang-bind-use-root>
```

Each use path starts with a root which indicates relative to what the path is located.

A `use` item may look for a path relative to: a specific library, or to the current module.p
- a specific library using the [library root] defined below, followed by a simple path
- the current module, by using a simple path

### Library relative use root [↵](#use-root-)
```
<use-lib-root> := [ <package-name> ] ':' [ <library-name> ]
<package-name> := [ <ext-name> '.' ] <ext-name>
                | '$' 'package'
<library-name> := <ext-name>
```

To access a a specified library, the declaration start by indicating the path to the library, this is called the _library relative use root_.

I is commonly shown as `package:library`, but these do not always need to explicitly be written down in the following usecases:
- the package can be left out if the path refers to the current package
- the library can be left out in 2 cases:
    - If there is no explicit package (i.e. the current package), it will refer to the current library or binary
    - If there is an explicit package, it will refer to the library within that package with that same name

The package name may contain 2 parts: an optional group name, and the actual name of the package.
For more info can be found [here](../package-structure.md).

> _Example_: leaving out certain parts of the library root with the following package structure can be seen in the table below:
> ```
> A (package)
> - Cur (lib)
> - A (lib)
> - C (lib)
> B (package)
> - B (lib)
> - D (lib)
> ```
> 
> And with the current library being `Cur`, the following paths will point to the following packages and libraries:
> 
> Use root | Package | Library
> ---------|---------|---------
> `:`      | `A`     | `Cur`
> `:C`     | `A`     | `C`
> `A:`     | `A`     | `A`
> `B:`     | `B`     | `B`
> `B:D`    | `B`     | `D`

> _Example_: the Minoa `std` packages' experimental library is accessed as `minoa.std:experimental`, where:
> - `minoa` is the group that the library belongs to
> - `std` is the package under which the library is distributed
> - `experimental` is the actual library.

### `$package` [↵](#use-root-)

`$package` is a special meta-variable that can be used within [meta blocks] (or any metafunction it maps to), that mpas to the package that defines the meta function.
It is only allowed to be used within a meta function.

Take the meta function defined in a `foo:bar` library, returning the following:
```
#{
    use $package:lib.item;
}
```
At all invocation sites of the meta function, this use will resolve to `use foo:lib.item;`

## Language binding import root [↵](#use-root-)
```
<lang-bind-use-root> := <string-literal>
```

Use items can also be used to import code included from another language, and their definitions within the current namespace.
This can be done by provided the path to the file within `""` (double quotes).
It will automatically generate binding to any symbol that is imported.

```
// Import all symbols define within `stdio.h` into the current namespace
use "stdio.h";
// Import all symbols define within `stdlib.h` into a `clib` namespace, relative to the current namespace
use "stdlib.h" as clib;
// Import the `isnan` function from the `math.h` header
use "math.h".isnan;
```

By default, the language of the source file will be based on the file name, but in certain cases, for example C and C++, multiple languages may use the same file extension.
In this case, the language of the file may also be specified by preceeding the file name in the quotes with a language named, followed by a `:`
```
// Import a C library
use "stdlib.h";
// Also import the C library, but explicitly specifies that the source file is C
use "c:stdlib.h";
```

If the language is explicitly specified, additional cmdline arguments may be added after the language, but before the `:`.
Example:
```
use "c -DNDEBUG=1:stdlib.h";
```

Depending on the langauge, this may also contain something other than just a file path.
This does require the language to be explicitly specified.

> _Example_: The builtin C importer allows simple preprocessors statements to be added.
> ```
> use `c:
> `#define C_DEF
> `#include "stdlib.h"
> `#include "stdio.h"`;
> ```

Additionally, these roots also allow for direct string interpolation.

> _Example_: Using the builtin C importer with string interpolation
> ```
> use "c:
> "#define NDEBUG { #compilation_mode == .release }
> "include \"stdlib.h\"";
> ```

> _Note_: By default, only C headers are supported, but other formats can added using a compiler extension.
>         For more info about custom importer, see the [Custom language bindings documentation].

> _Note_: For more info about what is supported by the C importer, see the [C importer compiler extension documentation]

## Use path [↵](#use-declarations-)
```
<use-path>       := <simple-path-no-start> [ <use-tree-trail> ]
<use-tree-tail>  := <use-glob>
                  | <use-grouping>
                  | <use-alias>
```

A use path follows the grammar of a simple path without a start.
It may also be succeeded by  a _tail_.

Each segment in the use path may refer to can be one of the following:
- Nameable [items]
- [Enum variants]
- Builtin types ([primitive types] & [string slices])
- [Attributes]
- [Metaprogramming items]

They can **not** be use to import the following:
- [associated items]
- [generic parameters]
- [local variables]
- paths with a [`Self` type]
- [tool attributes]
- items declared inside of [functions]

If no tail is added, the use item will solely import the named entity corresponding the the path specified.
Otherwise, it will do what the corresponding tail defines.

The following can be used as tails:
- [glob imports]
- [use groupings]
- [use aliases]

> _Note_: Any bindings generated by a use-declaration will extent to all [namespace] kinds

## Glob imports [↵](#use-declarations-)
```
<use-glob> := '.' '*'
```

A glob import is used to import all entities that are located in the namespace pointed to by the preceeding path.

```
mod inner {
    fn foo() {}
    fn bar() {}
}

// Import both `foo` and `bar` into the current scope
use inner.*;

fn main() {
    foo();
    bar();
}
```

Items and named imports are allowed to shadow names from glob impoorts within the same namespace.
Meaning that if a name is already defined, while also being imported via a glob, the name that is explicity defined takes precedences over the glob imported entity.

```
mod inner {
    fn foo() {}
    fn bar() {}
}

mod other {
    fn foo() {}
}

use inner.*;

// Shadows the `foo` imported by the wildcard above
use other.foo;

fn main() {
    foo(); // call `other.foo`
    bar(); // call `inner.bar`
}
```

## Use groupings [↵](#use-declarations-)
```
<use-grouping>    := '.' '{' <use-group-inner> { ',' <use-group-inner> }* [ ',' ] '}'
<use-group-inner> := <use-path>
                   | 'self' [ <use-alias> ]
```

A use grouping allows multiple entities to be imported from the location pointed to by the preceeding path.
Use grouping can be nested within each other.

```
// Imports the following:
// - foo.bar.baz
// - foo.bar.quux
// - foo.bar.quux.quuz
use foo.bar.{baz, quux.{self, quuz}}
```

An emty use-grouping is allowed, but this will not import anything, although the preceeding path will still be checked to be valid.

#### `self` imports [↵](#use-grouping-)

A `self` import is a special import that is only allowed inside of an import grouping.
It allows the entity at the sub-path before the grouping to be imported in addition to any other entities in the path.

```
mod inner {
    pub fn foo() {}
    pub fn bar() {}
}

mod example {
    // Imports both `inner` and `inner.foo`
    use :.inner.{self, foo};
    pub fn baz() {
        foo(); // Use the directly import `inner.foo`
        inner.bar(); // Use the `self` import, which refers to `inner`
    }
}
```

If the `self` import is the only element in an import grouping, the path will act the same as it would have been without the use grouping.
Meaning that `use foo.{self};` is close to equivalent to `use foo;`

## Use aliases [↵](#use-declarations-)
```
<use-alias> := 'as' <name>
```

A use alias allows an imported entity to be bound to another name.

```
// re-export `foo`, but use the alias `bar` to actually access it
use inner.foo as bar;

mod inner {
    fn foo() {}
}
```

### Underscore imports [↵](#use-aliases-)

Items can be imported without binding to a name by using an `_` (underscore) alias.
This is particularly useful to import a trait so that its methods may be used without importing the trait symbol, for example if the trait's symbol may conflict with another symbol.

```
mod inner {
    pub trait Foo {
        fn foo() {}
    }

    extend impl(T) T as Foo {}
}

// Import it as a `_`, so not to conflict with `Foo`
use inner::Foo as _;
struct Foo;

fn main() {
    let f = Foo;

    // We can now use `foo()`, as we let the compiler know of it's existence using the underscore import
    f.foo();
}
```

In additon, an `_` import can also be used to prevent conflicting imports.
This can for example by a meta-function which declares a use item.
```
// Meta-function which just outputs what is input twice
#cat2(use std as _;)

// This would expand to the following:
// ```
// use std as _;
// use std as _;
// ```
// No conflict, as neither is bound to an actual name
```

## Import ambiguity [↵](#use-declarations-)

Certain situations might occur where multiple entities with the same name are imported within the current scope, this will result in an ambiguity.
This might be cause in 3 different ways:
- 2 glob imports give access to symbols with the same name
- 2 imports each explicitly refer to an entity with the same name
- an import explicitly refers to an entity with the same name as another that is defined within the current scope.

The latter 2 will both result in a compiler error, regardless of wether the entities are being used.

The first option is a bit more complicated, as glob imports are allowed to import conflicting names, as long as the name is not used.

```
mod foo {
    pub struct Baz;
}

mod bar {
    pub struct Baz;
}

// Both are allowed to import `Baz`, as it is not used
use foo.*;
use bar.*;

fn main() {
    // This would result in an error, due to `Baz` being an ambiguous symbol
    // let b = Baz;
}
```

Unlike explicit imports, glob imports are allowed to import multiple identical names and be used, only when they all refer to the same entity.
The visibility of such an entity will then become the greatest visibility between the imports.
```
mod foo {
    pub struct Baz;
}

mod bar {
    pub use foo.Baz;
}

use foo.*;
use bar.*;

fn main() {
    // This is fine, as both `foo.Baz` and `bar.Baz` refer to the same entity
    let b = Baz;
}
```

Glob ambiguities can also be resolve by importing one of the entities explicitly.
The following would result in am ambiguous symbol, as both libraries contain `foo`.
```
use :lib0.*;
use :lib1.*;

fn main() {
    foo(); // ambiguous symbol, specify `lib0.foo` or `lib1.foo`
}
```
But it is possible to resolve this by importing `foo` explicitly:
```
use :lib0.*;
use :lib1.{*, foo}; // Still importing everything, but foo is now explicit

fn main() {
    foo(); // This is now `lib1.foo`
}
```

## Extension use [↵](#use-items)
```
<ext-use> := 'use' 'extension' <use-lib-root> [ '.' <use-path> ] ';'
```

An extension use is a variant on a use item which allows the importing of extension, which would normally be unavailable using a regular `use`.

An extension must appear within the [library root].

An extension use path must always start with a library.

> _Example_
> external:lib
> ```
> extension foo {
>     ...
> }
> ```
> 
> lib.mn
> ```
> use extension external:lib.foo;
> ```
```


[glob imports]:                                #glob-imports-
[library root]:                                #library-relative-use-root-
[`self`]:                                      #self-imports-
[use groupings]:                               #use-groupings-
[use grouping]:                                #use-groupings-
[use aliases]:                                 #use-aliases-
[Attributes]:                                  ../attributes.md
[tool attributes]:                             ../attributes.md#tool-attributes-
[generic parameters]:                          ../generics.md#generic-parameters-
[items]:                                       ../items.md
[functions]:                                   ../items/functions.md
[associated items]:                            ../items/implementations.md#associated-items-
[`#embed()`]:                                  ../langauge-items.md#embed- "Todo: replace with link to docs"
[Metaprogramming items]:                       ../metaprogramming.md
[meta blocks]:                                 ../metaprogramming/meta-utilities.md#meta-blocks-
[namespace]:                                   ../namespaces-scopes.md#namespaces
[local variables]:                             ../statements/variable-declarations.md
[Enum variants]:                               ../type-system/types/enum-types.md
[primitive types]:                             ../type-system/types/primitive-types.md
[`Self` type]:                                 ../type-system/types/self-type.md
[string slices]:                               ../type-system/types/string-slice-type.md
[visibility section]:                          ../visibility.md
[C importer compiler extension documentation]: #language-binding-import-root- "Placeholder"
[Custom language bindings documentation]:      #language-binding-import-root- "Placeholder"