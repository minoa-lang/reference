
# Module items
```
<module-item> := { <attribute>* } [<vis>] [ 'unsafe' ] "mod" <name> ';'
               | { <attribute>* } [<vis>] [ 'unsafe' ] "mod" <name> '{' { <module-attribute> }* { <item> }* '}'
```

A module is a container of zero or more items.

A module item introduces a a new named module into the tree of modules making up the current artifact.
Modules can be nested arbitrarily.

```
mod foo {
    mod bar {
        fn baz() {
            ...
        }
    }

    fn quux() {

    }
}
```

Modules share the same namespace as any other item located within the surrounding item or block.
Declaring a named type with the same name as a module in a scope is forbidden; that is, any item cannot shadow the name of a module in the scope and vice versa.
Items brought into scope with a `use` also have this restriction.

Modules can be marked as `unsafe`, this causes all items within this module that can be marked as `unsafe` to be marked as `unsafe`.

Modules are generally split up in 2 kinds.

## Inline modules [↵](#module-item-)

Inline modules are declared directly within another module and allows manual nesting within the file.

Inline modules are allowed to declare any file modules within them, but the path is interpreted differently.
An inline module also have a single segment path defined to name the sub-folder they would map to if they would have been file modules.

When using a nested module in a file:
```
mod bar {
    mod baz;
}
```
The following set of nested modules will be produces and there  corresponding filesystem structure when using the default module structure:

Module path     | Filesystem path          | File contentd
----------------|--------------------------|----------------
`:`             | `lib.mn` or `main.mn`    | `mod foo;`
`:.foo`         | `foo/mod.mn` or `foo.mn` | see code above
`:.foo.bar`     | `foo/mod.mn` or `foo.mn` | see code above
`:.foo.bar.baz` | `foo/bar/baz.mn`         |

## File modules [↵](#module-item-)

A file module refers to code located within an external file.
If no explicit path is defined for the module, the path to the file will mirror the logical module path.
All ancestor module path elements are represented by a path of nested directories within the artifact's source module.

The default naming of a sub-module is done in the following way:
- When the current file is a [module root](#432-module-roots-), the module file is located within the same directory as the module root's file.
- As a `mod.mn` file within a sub-directory with the module's name.

This is also the same order in which the compiler will scan for the module's file when no `path` attribute is provided

The following is an example of a set of nested modules and there corresponding filesystem structure when using the default module structure:

Module path | Filesystem path                  | File content
------------|----------------------------------|--------------
`:`         | `lib.mn` or `main.mn`            | `mod foo;`
`:.foo`     | `foo/mod.mn` or `foo.mn`         | `mod bar;`
`:.foo.bar` | `foo/bar/mod.mn` or `boo/bar.mn` |

File modules may only appear directly with an artifact's main module or a directly nested module, i.e. only modules nested inside of other modules.

## Path attribute [↵](#module-item-)

The directory and files used for loading a file module can be influenced using the `path` attribute.

If a `path` attribute is applied on a module that is not inside an inline module, the path is relative to the directory the source file is located in.

For example, with the following code in a file:
```
@path("foo.mn")
mod c;
```
will produce the following paths:

Module path    | `c`'s file location | `c`'s module path
---------------|---------------------|-------------------
`src/a/b.mn`   | `src/a/b/foo.mn`    | `:.a.b.c`
`src/a/mod.mn` | `src/a/foo.mn`      | `:.a.c`

For a `path` attribute inside an inline module, the relative location of the file path depends on the kind of source file the `path` attribute is located in.
If in a [module root](#432-module-roots-), the path is relative to the directory the module root is located in.
If it were to only use file modules, meaning that it will interpret all inline module modules as a directories.
Otherwise, it is almost the same, with the exception that the path starts with the name of the current module.

For example, for the following code:
```
mod inline {
    @path("other.mn")
    mod inner;
}
```
The path will be the following depending what file it is in:

Module path    | `inner`'s file location   | `inner`'s module path
---------------|---------------------------|-------------------
`src/a/b.mn`   | `src/a/b/inline/other.mn` | `:.a.b.inline.inner`
`src/a/mod.mn` | `src/a/inline/other.mn`   | `:.a.inline.inner`

The path that would be represented by an inline module may also be defined, as in the following example:
```
@path("foo")
mod inline {
    @path("bar.rs");
    mod inner;
}
```
`inner` would now be located within `foo/bar.rs`.

When a path is applied to an inline module, the path does not require an extension.
