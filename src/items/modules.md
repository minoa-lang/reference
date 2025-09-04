# Module items
```
<module-item> := { <attribute>* } [<vis>] [ 'unsafe' | 'safe' ] "mod" <name> ';'
               | { <attribute>* } [<vis>] [ 'unsafe' | 'safe' ] "mod" <name> '{' { <module-attribute> }* { <item> }* '}'
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

Modules share the same [namespace] as any other item located within the surrounding item or block.
Declaring a named type with the same name as a module in a [scope] is forbidden; that is, any item cannot shadow the name of a module in the scope and vice versa.
Items brought into scope with a `use` also have this restriction.

Modules can have a [visibility] assigned to them, this visibility will be propagate to all items contained within the module.
If a module were to contain any other module however, it will not take over the visibiliyt, i.e. visibility is only propagated to non-module items.
Visibility will only be propagated whenever an item within the module does not have an explicit visibility.

In addition, they can also be marked as `unsafe`, this causes all items within this module that can be marked as `unsafe` to be marked as `unsafe`.
The opposite is also possible, marking a module as `safe`, this will essentially strip the `unsafe` marker from function inside of an `unsafe` item.

> _Todo_: Remove `foo/mod.mn`, in preference for `foo.mn` and an associated `foo/` folder

## File modules [↵](#module-item-)

A file module refers to code located within an external file.
If no explicit path is defined for the module, the path to the file will mirror the logical module path.
All ancestor module path elements are represented by a path of nested directories within the artifact's source.

The default location of a sub-modules is decided as following:
- when the current file is a [main module], the module file is located within the same directory as it
- otherwise, the file will be located in a sub-folder in the same directory of the parent module, and with the same name.

The module file itself is the name of a module, followed by the `.mn` extension.

The following is an example of a set of nested modules and there corresponding filesystem structure when using the default module structure:

Module path | Filesystem path       | File content
------------|-----------------------|--------------
`:.`        | `lib.mn` or `main.mn` | `mod foo;`
`:.foo`     | `foo.mn`              | `mod bar;`
`:.foo.bar` | `foo/bar.mn`          |

> _Note_: File modules may only appear directly with an artifact's main module or a directly nested module, i.e. only modules nested inside of other modules.
>         In any other location, an inline modules has to be used.

## Inline modules [↵](#module-item-)

Inline modules are declared directly within another module and allows manual nesting within a single file.

Inline modules are allowed to declare any file modules within them, but their path will be interpreted differently.
A sub-folder with the same name as the inline module will be added to the path when looking for the file module.

They add a segment to the path for any associated modules, acting as if they would already be in a sub-folder where they would have been if they were a file module.

When using a nested module in a file:
```
mod bar {
    mod baz;
}
```
The following set of nested modules will be produces and their corresponding filesystem structure when using the default module structure:

Module path     | Filesystem path       | File content
----------------|-----------------------|----------------
`:.`            | `lib.mn` or `main.mn` | `mod foo;`
`:.foo`         | `foo.mn`              | see code above
`:.foo.bar`     | `foo.mn`              | see code above
`:.foo.bar.baz` | `foo/bar/baz.mn`      |

## Path attribute [↵](#module-item-)

The directory and files used for loading a file module can be influenced using the `path` attribute.

If a `path` attribute is applied on a module that is not inside an inline module, the path is relative to the directory the source file is located in.

For example, with the following code in a file:
```
@path("foo.mn")
mod c;
```
will produce the following paths:

Module path   | `c`'s file location | `c`'s module path
--------------|---------------------|-------------------
`src/main.mn` | `src/foo.mn`        | `:.c`
`src/a.mn`    | `src/a/foo.mn`      | `:.a.c`

For a `path` attribute inside an inline module, the relative location of the file path depends on the kind of source file the `path` attribute is located in.
The path would take into account which sub-directories the inline modules would be located in, if they were to have been file modules.
This path is relative to either:
- if the inline module is defined within a [main module], the artifact's source directory, 
- otherwise, to a sub-directory with the same name as the module

For the following code:
```
mod inline {
    @path("other.mn")
    mod inner;
}
```
The path will be the following depending what file it is in:

Module path   | `inner`'s file location | `inner`'s module path
--------------|-------------------------|-----------------------
`src/main.mn` | `src/inline/other.mn`   | `:.inline.inner`
`src/a.mn`    | `src/a/inline/other.mn` | `:.a.inline.inner`

A path attribute may also be defined on an inline item, this changes the name of the sub folder that the module would have corresponded to.
When applied to an inline module, the file extension can be left out

For the followin code
```
@path("foo")
mod inline {
    @path("bar.rs")
    mod inner;
}
```
`inner` would now be located within `foo/bar.rs`.



[namespace]:   ../namespaces-scopes.md#namespaces
[scope]:       ../namespaces-scopes.md#scopes
[main module]: ../package-structure.md#main-module-
[visibility]:  ../visibility.md