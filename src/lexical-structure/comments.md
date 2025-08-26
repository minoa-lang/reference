
# Comments

Comments are used to add additional info or documentation to code.

Only line comments are supported, these begin at a given token depending on the type of comment, and complete at the end of the current line.
The decision was made not to support block comments, as nestable block comments complicate parsing, and they can cause some edgecases where the end token can appear within a string inside of the comment.

There are 2 type of comments:

## Regular comments [↵](#comments-)
```
<regular-comment> := '//' {? any unicode character ?}* <new-line>
```

Regular comments are used to just add additional info to code, or used to comment out code, allowing the code is still be in the file, but interpreted as a comment.

Some elements that are in doc comments are also available in a regular comment and can be uses by tooling, but these will not end up in generated documentation.
These elements are:
- [General notes & exhortations](#general-notes--exhortations-)
- [Issue](#issue-)

In addition, a regular comment only element is also supported: a mark.
A mark is used for easier code navigation in tools that support it.
A mark is always provided in a comment as `// \mark name`.

Regular comments are stored as metadata associated with tokens and are not tokens by themselves.

## Doc comments [↵](#comments-)
```
<doc-comment> := <doc-comment-start> {? any unicode character ?}* <new-line>
<doc-comment-start> := '///' | '//!'
```

Doc(umentation) comments are used to provide documentation for a given item.

In addition to block and line versions, documentation comments can also either apply to:
- the item directly below them, just called 'regular doc comments', or
- if a module is in its own file, to the module they are in, called 'top-level doc comments',

These are differentiated by how the comment starts, regular doc comments start with 3 forward slashes, i.e. `///`, while top-level doc comments start with 2 forward slashed, followed by an exclaimation mark, i.e. `//!`

A top-level doc comment within the library's root module also apply directly to the library.

During parsing, these get converted to their relavent attributes, i.e.
- regular doc comments like `/// Foo` and `/** Foo */` map to `@doc("Foo")`
- module-level doc comments like `//! Bar` and `/*! Bar */` map to `@!doc("Bar")`

A carriage return (CR) is not allowed within a doc comment, except when followed immediately by a newline.

Regular doc comments are only allowed before items, and module-level doc comments are only allowed before any item within a module, anything else will cause an error.

#### `doc` attribute [↵](#doc-comments-)

Since doc comment map into a `doc` attribute, all elements within the format described above are converted to a doc attribute.
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

[Doc comment format](./doc-comment-format.md)