# Package structure

A package represents the upper level of a hierarchy of artifacts and the main unit of distribution.

Packages themselves are not the direct result of compilation, but play an integral part in code organization.
Howevver, packages do define how code is imported, as it is the base on which all import paths are based..

A package can contain any number of artifacts, allowing related code to be shared as a single unit,
meaning that if a project is split up in modularized components, they can still be easily distributed, without having to result to sub-naming.

### Groups [↵](#package-structure)

Packages may also be part of a group, this is a logical grouping of packages, but unlike packages, they are an optional part of code distribution.
The main purpose of groups is allowing an organization combine their packages under the organization's name.
This also allows muliple organizations that each have their own group, to have similar package name.

An example of how groups can be used:
```
Minoa package registry
├─CompanyA
│ ├─ProductA
│ │ ├─Binary
│ │ └─Dylib
│ └─ProductB
│   └─Binary
├─OrgA
│ └─ProductA
│   └─Binary
└─ProductA
  └─Binary
```

In the above example, 3 packages are all named 'ProductA', but they can be distinguished as:
- `CompanyA.ProductA`: Product of a company
- `OrgA.ProductA`: Product of an organization, independent from the company
- `Product`: A seperate product made by a independent developer

## Artifacts [↵](#package-structure)

Artifacts, unlike packages, are the direct result of a compilation process or stage.

An artifact consts out of 3 distinct types:
- binaries
- static libraries
- dynamic libraries

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
Each module is allowed to have multiple sub-modules.

### Main module [↵](#43-modules-)

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
