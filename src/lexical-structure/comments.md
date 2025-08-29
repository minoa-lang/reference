
# Comments

Comments are a kind of _trivia_ used to add additional info or documentation to code.

Only line comments are supported, these begin at a given token depending on the type of comment, and complete at the end of the current line.
The decision was made not to support block comments, as nestable block comments complicate parsing, and they can cause some edgecases where the end token can appear within a string inside of the comment.

> _Note_: Then language also reserve all sequences of character directly located after `//`, if they are not separated by whitespace.
>         Using any undefined sequence will result in an error.

## Regular comments [↵](#comments-)
```
<regular-comment> := '//' {? any unicode character ?}* <new-line>
```

Regular comments are used to just add additional info to code, or used to comment out code, allowing the code to still be in the file, but to be interpreted as a comment.

Some elements that are in doc comments are also available in a regular comment and can be used by tooling, but these will not end up in generated documentation.
These elements are:
- [General notes & exhortations]
- [Issue]

In addition, a regular comment-only element is also supported: a mark.
A mark is used for easier code navigation in tools that support it.
A mark is always provided in a comment as `// \mark name`.

Regular comments are stored as _trivia_ associated with tokens and are not tokens by themselves.

## Doc comments [↵](#comments-)
```
<doc-comment>         := <doc-comment-start> {? any unicode character ?}* <new-line>
<doc-comment-start>   := '///' | '//!'
```

Doc(umentation) comments are used to provide documentation for a given item.

Documentation comments can apply to either:
- the item directly below them, just called _regular doc comments_, or
- if a module is in its own file, to the module they are in, called _top-level doc comments_

These are differentiated by how the comment starts, regular doc comments start with 3 forward slashes, i.e. `///`, while top-level doc comments start with 2 forward slashed, followed by an exclaimation mark, i.e. `//!`

> _Note_: A top-level doc comment within the library's root module applies directly to the library.

During compilation, these are interpreted as their relavent attributes, i.e.
- regular doc comments like `/// Foo` and `/** Foo */` map to `@doc("Foo")`
- module-level doc comments like `//! Bar` and `/*! Bar */` map to `@!doc("Bar")`

But at the same time, they are stored as _trivia_, similar to how regular comments are stored

A carriage return (CR) is not allowed within a doc comment, except when followed immediately by a newline.

Regular doc comments are only allowed before items, and module-level doc comments are only allowed before any item within a module, anything else will cause an error.

It's format is defined [here](./doc-comment-format.md)

### Suffix description [↵](#doc-comments-)
```
<suffix-desc-comment> := '//<' {? any unicode character ?}* <new-line>
```
In addition to regular documentation comments, a suffix description may also be added.
This is indicated using `//<`.

This comment type is a variant of a documentation comment, and defined a short documentation for the item preceeding it.

> _Note_: This style of comment is mainly meant for fields of a type.

### `doc` attribute [↵](#doc-comments-)

Since doc comment map into a `doc` attribute, all elements within the format described above are interpreted as a doc attribute.
This attribute may be split up into a sequence of smaller `doc` attributes. Each separate attribute will be interpreted as its own separate line, meaning that:
```
@doc("This")
@doc("does")
@doc("a thing")
```
will be equivalent to
```
//! This
//! does
//! a thing
```

A `doc` attribute may only contain a string literal, or a set of specific arguments

Whenever a `doc` attribute does not have a specific section specified, and only contains a string, of a known set of doc attributes.

The below section also explains the mapping between a given doc element and it's associated sub-attribute.

> _Note_: It's generally prefered to use comments instead of explicit `doc` attributes where possible for readability

[General notes & exhortations]: ./comments/doc-comment-format.md#general-notes--exhortations-
[Issue]:                        ./comments/doc-comment-format.md#issue-