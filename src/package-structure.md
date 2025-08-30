# Package structure

A package represents the upper level of a hierarchy of artifacts and the main unit of distribution.
In addition, they are also a way of versioning, by distributing a set of differently versioned artifacts.
As it is expected that all artifacts within a given version of a package are all interoperable.

Packages themselves are not the direct result of compilation, but play an integral part in code organization.
However, packages do define how code is imported, as it is the base on which all import paths are based.

A package can contain any number of artifacts, allowing related code to be shared as a single unit,
meaning that if a project is split up in modularized components, they can still be easily distributed, without having to result to sub-naming.

### Groups [↵](#package-structure)

Packages may also be part of a group, this is a logical grouping of packages, but unlike packages, they are an optional part of code distribution.
The main purpose of groups is allowing an organization combine their packages under the organization's name.
This also allows muliple organizations that each have their own group, to have similar package name.

> _Example_:
> Groups can be used in the following way:
> ```
> Minoa package registry
> ├─CompanyA
> │ ├─ProductA
> │ │ ├─Binary
> │ │ └─Dylib
> │ └─ProductB
> │   └─Binary
> ├─OrgA
> │ └─ProductA
> │   └─Binary
> └─ProductA
>   └─Binary
> ```
> 
> In the above example, 3 packages are all named 'ProductA', but they can be distinguished as:
> - `CompanyA.ProductA`: Product of a company
> - `OrgA.ProductA`: Product of an organization, separate from the company
> - `ProductA`: A seperate product made by a independent developer

## Artifacts [↵](#package-structure)

Artifacts, unlike packages, are the direct result of a compilation process or stage, as well as the main unit of versioning and runtime loading.

An artifact consts out of the following distinct types:
- binaries
- static libraries
- dynamic libraries

In addition, a so-called 'mixed-artifact' also exists.
This combines a libraries library and a binary that use the same underlying source, but with a different main modules.

> _Note_: Static and dynamic libraries use the same main module, but are compiled differently

Artifact themselves are made up from modules.

### Binaries [↵](#artifacts-)

Binaries are the resulting runnable executables, these are not meant to be 'imported', as they miss all the data required for it.
These can be delivered together with binaries, not only to jjjbe used as the final application, but also tools used for additional functionality.

### Static ibraries [↵](#artifacts-)

A static library is a library that is meant to be linked into any code using it.
It contains all info needed to 'import' and use it in other code, including the bytecode for all the relavent issues.

If possible, the compiler can inline any code within the static library.

### Dynamic ibraries [↵](#artifacts-)

A dynamic library is a library that is meant to be referenced by code linking to it, unlike a static binary, this is not linked directly into the code, but live as their own file right next to it.
Dynamic libraries actually generates 2 resulting file: a Minoa library and a OS-specific dynamic library.
The Minoa library is similar to those produced for static libraries, but does not contain all data that the static library has, i.e. they only include what is needed to successfully build and to reference the dynamic library in the code using it.

## Modules [↵](#package-structure)

A module generally represents a single file or inlined module definition (if a file is not directly included within another file).
Each module represents another segments within a namespace.

Every source file represents its own module, but not every module has an associated source file.

Each module is allowed to have any numnber of multiple sub-modules.

More info about modules can be found [here](./items/modules.md).

### Main module [↵](#modules-)
```
<artifact> := [ <byte-order-marker> ] [ <shebang> ] { <doc-comment> }* { <attributes> }* { <item> }*
```

Each artifact has its own main module, which by default uses the following files:
- binaries: `main.mn`
- static and dynamic libraries: `lib.mn`

These module do not have a namespace from the point of view from the library, as they are essentially code that is located at the root of the library.

### Module roots [↵](#modules-)

A module root is a specially named file which indicates that its sub-modules are located within the same directory as it, instead of in a sub-directory.

A module root is one of the following:
- A binary main module, i.e. `main.mn` when in the binary's root source folder
- A library main module, i.e. `lib.mn` when in a library's root source folder
- `mod.mn` when in a module's sub-folder

### Module organization [↵](#modules-)

Modules are organized with a tree as part of a given library, where the [main module] represents the root of the tree.


> _Example_:
> The following folder structure:
> ```
> - src/
>   - lib.mn
>   - a.mn
>   - b.mn
>   - b/
>     - d.mn
>   - c/
>     - mod.mn
>     - e.mn
> ```
> 
> results in the following module structure
> ```
> library
> ├─a
> ├─b
> │ └─d
> └─c
>   └─e
> ```

## Main function [↵](#package-structure)

The main function is a special function which defined the entry point of a binary artifact.
It must be visible from the [main module] of the binary.

The main function takes no arguments, and must return a type implementing the `Termination` trait.
This is meant to allow the main function to return any type that implements the trait.

Some examples of possible main functions are
```
fn main() {}
```
```
fn main() -> ! {

}
```
```
fn main() -> impl Termination {

}
```

> _Todo_: Make sure examples are valid, i.e. fix implementation

The main function may be an import from another library or a module in the current library by aliasing a function meeting the requirements as `main`.

```
mod foo {
    fn bar() {
    }
}

use :.foo.bar as main;
```



[main module]: #main-module-