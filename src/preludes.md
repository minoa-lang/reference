# Preludes

A prelude is a special module in a project which contains imports that are implicitly included with a project.
A prelude exists next to the [main module], and is otherwise unrelated to it.

They are available in every file that uses the library, and all public re-exports can be used as if they were explicltly imported.

If an explicit symbol with the same name as one in a prelude is imported, then the prelude symbol will be shadowed and a warning will be generated.

## `core` prelude

The `core` preluded is always included within every artifact, and allows access to any core items require by the langauge to operate correctly.
Inclusion of this prelude cannot be disabled.

Any `core` items that are shadowed do not generate warnings by default, but can be enabled.

## `std` prelude

The `std` prelude is automatically included by the build tools, unless explicitly disabled.

## custom preludes

Libraries can add their own custom prelude, by having a `prelude.mn` along side the [main module].
This file may only include public re-exports and cannot contain any other code, other than [`when` items].

[main module]:  ./package-structure.md#main-module-
[`when` items]: ./items/when-items.md