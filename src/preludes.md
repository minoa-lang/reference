# Preludes

A prelude is a special file in a project which contains imports that are implicitly included with a project.
It is avaiable within every file that uses the library, and all public re-exports can be used as if they were explicilty imported.

If an explicit symbol with the same name as one in a prelude is imported, then the prelude symbol will be shadowed by it, and no errors will be generated

## 26.3. `core` prelude [↵](#26-preludes-)

The `core` prelude is included within every single library, and allow access to any core items required by the language to operator.

This does not however mean that every single item in the core library will be in the prelude.

## 26.2. `std` prelude [↵](#26-preludes-)

The `std` prelude is automatically included by the build tools and is generally always available.
If needed, the build system can be told to not include this prelude

## 26.3. Custom preludes [↵](#26-preludes-)

Libraries can add their own custom prelude, by having a `prelude.mn` file next to the `lib.mn` file.

This file is solely used for public re-exportimg imports, and cannot contain any other code, other than [`when` items](./items/when-items.md) with other re-exports.
