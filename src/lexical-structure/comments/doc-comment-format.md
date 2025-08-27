
# Doc comment format

Doc comments follow a specific format, which starts on either the first line doc comment, or the first or second line of a block doc comment (which ever is the first to contain text)

Doc comments generally don't follow a specific format, except that the first line of text before the first v

The general format is the following:
```
/// [ Short description what the item is/does ]
///     
/// [ Receiver ]
/// [ Implicit parameters ]
/// [ Parameters ]
/// [ Return ]
/// 
/// [ Long description what the item is/does ]
```

Each part of the documentation is known as a doc element, which with the exception of the short and long description, must start at the beginning of a line and is similar to escape code, but where the text following the backslash (`\`) defined the element.

Some elements allow additional data to be provided later on in the line, by starting them with a `\`, or as values in a comma-separated list.
Check each element for info about what additional values they support.

Doc comments will trim the start based each line based on the depth the first character on the first line with text is located. This means that if a lower line starts on an earlies column, an error will be generated.

Support of some elements will depend on the viewer used (e.g. browser).

> _Note_: Any 3rd party document generation tooling (doc-gen) is expected to follow this specification.

> _Note_: Additional elements may be added later

## Short description [↵](#doc-comment-format)

The short description is generally a simple 1 line sentence describing the basic function of an item.

It is mapped to the `short` sub-attribute, i.e. `@doc(short="short desciption here")`

## Long description [↵](#doc-comment-format)

The long description is a detailed description describing the functionality of an item.

A long description follows a flavor of the .md/mark-down spec, but with some additional features to aid in the generation of documentation.
These elemens will be explained further down below.

This element is created when either no sub-attribute is used, or a value is given to the `long` sub-attribute, i.e. `@doc("long description here")` or `@doc(long="long description here")`

## Including external text [↵](#doc-comment-format)

Whenever text is stored in an external file to the comment, this can be included using a special `include_str` element.
It may also be passed to a `doc` (sub-)attribute where a string literal is allowed.

the syntax of this element is either: `include_str(source_file)`, where `source_file` is itself a string literal with relative path pointing to the file that contains the text.
This path is relative to the file in which the documentation is located.

Text may be inserted anywhere inside of text by surrounding the element with `\{` and `}`

For example:
- `@doc(include_str("./example.md"))`
- `@doc(... = include_str("./example.md"))`
- `/// some text in \{include_str("./example.md")}`

## Item links [↵](#doc-comment-format)

An item link is an extension of markdown links, allowing the links to refer to a specific item within code.
These links may refer to any item accessable from the scope the comment is in, using their paths, including `Self`, `self`, `super`, `lib`, and `package`.
The links are relative to the scope they are located in.

Links may also refer to builtin types.

> _TODO_: Add example based on std lib

URL fragments are also allowed

> _TODO_: Add example based on std lib, pointing to a segment, i.e. path#segment

When linking a function, they have to be differentiated using their parameter labels, i.e. ``[`foo()`]`` and ``[`foo(_:_)`]`` are distinct.

## Receiver [↵](#doc-comment-format)

The receiver elements allows additional info about a receiver to be added.

Receiver info can be provided in a comment as `/// \receiver {info}`, or in a sub-attribute as `@doc(receiver="{info}")`.

The info can be split across multiple lines by having the following lines indented.

## Inferred parameter [↵](#doc-comment-format)

The inferred parameter element allows additional info about an inferred parameter to be added.

Inferred parameter info can be provided in a comment as `/// \infer_param {name} {info}`, or in a sub-attribute as `@doc(infer_param(name="{name}", info="{info}"))`

The info can be split across multiple lines by having the following lines indented.

## Parameter [↵](#doc-comment-format)

The parameter element allows additional info about a parameter to be added.

Parameter info can be provided in a comment as `/// \param {name} {info}` or in a sub-attribute as `@doc(param(name="{name}", info="{info}"))`.

Information about the default value can in addition be provided in a comment as `\default {info}`, or in a sub-attribute as `@doc(param(..., default=""))`.

The info can be split across multiple lines by having the following lines indented.

## Return [↵](#doc-comment-format)

The return element allows additional info about a item's return type to be added.

Return info can be provided in a comment as `/// \return {info}`, or in a sub-attribute as `@doc(return="info")`.

The info can be split across multiple lines by having the following lines indented.

## Named return [↵](#doc-comment-format)

The named return element allows additional info about a item's return to be added if it contains named return values.

Return info can be provided in a comment as `/// \named_return {name} {info}`, or in a sub-attribute as `@doc(named_return(name="{name}", info="info")`.

The info can be split across multiple lines by having the following lines indented.

## Since [↵](#doc-comment-format)

The since element allows the version of the library since when an item was added to be specified.

Since info is set in a comment as `/// \since {version}`, or in a sub-attribute as `@doc(since="{version}")`

## Pre-condition contract [↵](#doc-comment-format)

The pre element allows additional info about an item's pre-condition contract to be added.

Pre-condition info can be provided in a comment as `/// \pre {info}`, or in a a sub-attribute as `@doc(pre={info})`.

The info can be split across multiple lines by having the following lines indented.

## Post-condition contract [↵](#doc-comment-format)

The post element allows additional info about an item's post-condition contract to be added.

Post-condition info can be provided in a comment as `/// \post {info}`, or in a a sub-attribute as `@doc(pre={info})`.

The info can be split across multiple lines by having the following lines indented.

## Invariant contract [↵](#doc-comment-format)

The pre element allows additional info about an item's invariant contract to be added.

Invariant info can be provided in a comment as `/// \invar {info}`, or in a a sub-attribute as `@doc(invar={info})`.

The info can be split across multiple lines by having the following lines indented.

## Complexity [↵](#doc-comment-format)

The complexity element allows additional info about an item's complexity to be added.
Complexity is generally written in a big O notation.

Complexilty info can be provided in a comment as `/// \complexity {info}`, or in a sub-attribute as `@doc(complexity="{info}")`.

## Important [↵](#doc-comment-format)

The important element allows additional info about what is important when using an item. This info will be clearly marked in the documentation.

Important info can be provided in a comment as `/// \important {info}`, or in a a sub-attribute as `@doc(important="{info}")`.

The info can be split across multiple lines by having the following lines indented.

## Warning [↵](#doc-comment-format)

The warning element allows additional info about what a user should care to avoid when using an item. This info will be clearly marked in the documentation.

Warning info can be provided in a comment as `/// \warning {info}`, or in a a sub-attribute as `@doc(warning="{info}")`.

The info can be split across multiple lines by having the following lines indented.

## Attention [↵](#doc-comment-format)

The attention element allows additional info about what a user should pay attention to when using an item.
This is more general then important and warning, which specifically state what is important when using the item, and what should be avoided, respectively.
This is therefore also not hightlighted like those 2 elements are.

Warning info can be provided in a comment as `/// \attention {info}`, or in a a sub-attribute as `@doc(attention="{info}")`.

The info can be split across multiple lines by having the following lines indented.

## Errors [↵](#doc-comment-format)

The errors element can be in one of two forms:
- a general overview of all possible errors that can be returned, or
- info a specific error that's in the possible errors

This is mainly useful in conjunction with a `Result` return type.

General error info can be provided in a comment as `/// \errors {info}`, or in a sub-attribute as `@doc(errors="{info}")`.

While providing info about a specific error, it can be provided in a comment as `/// \error {name} {info}`, or in a sub-attribute as `@doc(error(name="{error}", info="{info}"))`.
The `{error}` value will refer to a specific variant of the return error, while `{info}` is optional, and when left out, it will add the short description of the variant specified in `{error}`.

The info can be split across multiple lines by having the following lines indented.

## Issue [↵](#doc-comment-format)

The issue element allows an issue to be associated with an item.

An issue can be provided in a comment as `/// \issue {issue}`, or in a sub-attribute as `@doc(issue={issue})`.
When `{issue}` is just a number, it will generated a link based on the [issue tracker base], otherwise an issue can be provided with a URL to the specific issue.

## General notes & exhortations [↵](#doc-comment-format)

This is a collection of elements with commonality between them, as they are meant to communicate additional info about code between authors and/or users.

All of these elements follow the same notation, with an additional author or authors.
These can be provided in a comment as `/// \{elem} {info}` or `/// \{elem}({author}) {info}`, or in a sub-attribute as `@doc({elem}="{info}")` or `@doc({elem}(authors={authors}, info={info}))`.

Authors are a comma separated list in a comment, and either a string or array literal of strings in a sub-element, depending if there is 1 or multiple authors.
Each author follows the following format `name < example@example.com >`, where `name` is the authors username and `< example@example.com >` is replaced by the mail associated with the author, surrouneded by `<>`, the mail is entirely optional.

The possible elements are the following:
- `bug`: Adds additional info about a bug in the item/code and any authors associated with it
- `experimental`: marks an item/code as experimental, with optional info and authors that are relevant to it.
                  If no info needs to be added, it can be simply provided in a comment as `/// \experimental`, or in a sub-attribute as `@doc(experimental)`
- `note`: Adds additinal info about a given item/code
- `remark`/`remarks`: Adds remarks/criticism about a given item/code
- `todo`: Adds info about additional work that needs to be done on the item/code
- `perf`: Adds additional remarks abour the performance of an item/code

## Favicon [↵](#doc-comment-format)

The favicon element specifies a favicon used for the documentation.
By default, no favicon will be set.
A favicon is only allowed in a doc comment applying to the root of a library.

A favicon may point to either a url or a local path, when pointing to a local path, the favicon will be included in the generated documentation.

Favicon are set in a comment as `//! \favicon {path}`, or in a sub-attribute as `@!doc(favicon="{path}")` or `@!doc(favicon("{path}"))`.
To explicitly indicate what the path is in a sub-attribute, `favicon(path = "{path}")` can be used instead.

In addition, a favicon may also specify the sizes contained within the file pointed to by the given path. 
It can be added using `, sizes="{sizes}"`, in a sub-attribute this will look like `@!doc(favicon("{path}", sizes="{sizes}"))`.
Where `{sizes}` is a space separated list of `<width>x<height>` values, with `<width>` and `<height>` being values ranging from 1 to 256, e.g. "32x32 64x64".

The type of the icon will be derived from the file extension of a given path, which can be one of the following:
- `.ico`, corresponding to `type="image\x-icon"`
- `.gif`, corresponding to `type="image\gif"`
- `.png`, corresponding to `type="image\png"`
- `.svg`, corresponding to `type="image\svg+xml"`

A favicon will correspond to the following html: `<link rel="icon" type="{}" sizes="{sizes}" href="{path}">`, where `sizes` will be left out when none are specified, and the values between `{}` are replaced with their respective values.

If multiple favicons are specified, the viewer decided which of the favicons is used.

## Logo [↵](#doc-comment-format)

The logo element specifies the logo used within the documentation.
By default, no logo will be et.
A logo is only allowed in a doc comment applying to the root of the library.

A logo may point to either a url or a local path, whe npointing to a local path, the favicon will be included in the generated documentation.

Logos are set in a comment as `//! \logo {path}` or in a sub-attribute as `@!doc(logo="{path}")` or `@!doc(logo("{path}"))`.
To explicitly indicate what the path is in a sub-attribute, `logo(path = "{path}")` can be used instead.

In addition, a logo may also specify the size is should show up as. This can be done by specifying a width and/or height it should show up as.
This will not un-uniformly scale the image to make both the width and height to match if the provided log does not have the same aspect ratio, but will instead uniformly scale the image until either the width or the height have hit the desired size.
They can be added using `, width={width}` and `, height={height}`, in a sub-attribute, this will look like `@!doc(logo("{path}", width={width}, height={height}))`

Supported image types are:
- PNG
- APNG
- GIF
- JPEG
- SVG
- WebP

A logo will correspond to the following html: `<img src='{path}' alt='logo' width='{width}'>`, if `width` will be defaulted to the width of thelogo when not explicitly specified, and the values between `{}` are replaced with their respective values.

If multiple logos are specified, an error will be generated.

> _Note_: the specifics of how the logo will be located in the docs still needs to be determined, including the maximum size

## Playground [↵](#doc-comment-format)

The playground element specifies how code in the documentation may be run using a button within a code block.
By default, no playground is used, ano no buttons allowin execution will show up.
A playgound is only allowed in a doc comment applying to the root of a library.

The playground can be set in comments as `//! \playground {playground}` or in a sub-attribute as `@!doc(playground({playground}))`.
Where `{playground}` can be either:
- `embedded` which will run the code in a documentation provided playground, or
- `url={path}` which will run the code in an online playground

## Issue tracker base [↵](#doc-comment-format)

The issue tracker base defines the base URL which can be uses by issue element to construct a url from the issue number.
By default, no url is assigned and issue elements must declare a full path.
An issue tracker base is only allowed in a doc comment applying to the root of the library.

The issue tracker base can be set in comments as `//! \issue_tracker_base {url}` or in a sub-attribute as `@!doc(issue_tracker_base="{url}")`.

## No source [↵](#doc-comment-format)

By default, the docs will include the source code, adding links for each item to the relavent source code.
Using this element, this can be turned off, meaning that source code won't be included and no links to it will be generated.

This element can be applied to the library as a whole, or to specific items or modules.

No source is set in a comment as `/// \no_source` or in an attribute as `@doc(no_source)`.

## `inline` and `no_inline`

The inline attribute are applied to `use` statements and control how documentation shows up. The overwrite the default behavior set when generating documentation.

This can only be specified as a sub-attribute, which is defined as `@doc(inline)` or `@doc(no_inline))`.

Assuming the following code:
```
// (1)
pub use bar::Bar;

/// Docs for the 'bar' module
mod bar {
    /// Docs for the 'Bar' struct
    pub struct Bar;
}
```

If no attribute appears at '(1)', then the page will add `pub use bar::Bar` to its `use` section, which will have a link to the item within said module.
If instead the `@doc(inline)` attribute is added, the `use` will not appear with the `use` section and the documentation will be generated directly within the documentation for the surrounding module, instead of on its own page.
This does mean the documentation for `bar` will located in the module's page, only the docs for the struct `Bar`.

If instead `bar` would be private, as in the following code:
```
// (1)
pub use bar::Bar;

/// Docs for the 'bar' module
mod bar {
    /// Docs for the 'Bar' struct
    pub struct Bar;
}
```
`Bar`'s documentation would be generated within the module's page when the doc-gen is specified to generate documentation for publicly accessable items that are located inside private items.
In this case, the `no_inline` attribute will add `Bar` within the `use` section, but will not link to anything, as the containing module is private, assuming the doc-gen is not told to generate docs for private items.

## `hidden` [↵](#doc-comment-format)

Any item that is annotated with this attribute will be hidden from the documentation, unless the doc-gen is told to generate hidden docs.

This can only be specified as a sub-attribute, which is defined as `@doc(hidden)`

## `alias` [↵](#doc-comment-format)

An alias element specifies an alias when searching for a given item, meaning that searching for this alias will result in the item the comment applies to. Using the actual name of the item still works.

An alias is set in a comment as `//! \alias {name}` or in a sub-attribute as `@doc(alias="name")`.

A use case for this can be seen in the following code:
```
pub struct Foo {
    ...
}

impl Foo {
    pub fn do_ffi_thing(&mut self) -> i32 {
        unsafe { ffi::lib_name_act_function(...) }
    }
}
```
The function is now wrapped for convencience, but now searching for `lib_name_act_function` will not result in any function within the documentation.
Adding the alias element `@doc(alias="lib_name_act_function")` to it allows the item to be looked up using the original name of the underlying function, and the user will fine the correct method to use.

> _Note_: This example assumes that `ffi` is not a documented module.

# Examples [↵](#33-comments-)

Below are some examples of how doc comments can be used.

> _Note_: Not all possible elements are shown in these examples

> _Todo_: Add more examples

# Library description [↵](#363-examples-)

By placing comments in the library's root module, we can have these comments specify the description for the library

```
//! Short description for the libary
//!
//! Long description for the library (as we are in the root module)
//!
//! \favicon path/favicon.ico, sized="32x32 64x64"
//! \logo path/logo.png
//! \playground embedded
// or
//! \playground https://www.example.com/playground
//! \issue_tracker_base https://www.example.com/repo.git/issues
```
or alternatively, this can also be
```
@!doc(short="Short description for the library")
@!doc("")
@!doc("Long description for the library (as we are in the root module)")
@!doc(favicon=(path=path.favicon.ico, sized="32x32 64x64"), logo="path/logo.png")
```

## Documenting a module [↵](#363-examples-)

When a module is in its own file, we can use top-level comment:
```
//! Short description of the module that is represented by the current file.
```

But when we are in a nested module, we need to put regular doc comments before them:
```

/// Description of `foo`.
mod foo {
    //! This will result in an error, as we aren't at the top level of the current file.
}
```
This is simila for doc attributes
```
@doc("A description for `foo`")
mod foo {
    @!doc("This will also result in an error")
}
```

[issue tracker base]: #issue-tracker-base-