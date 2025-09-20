# Introduction

> Current version: 0.1

This book contains the current language reference for the Minoa language.

This reference is an overview of the Minoa langauge in its current state and is written for the development of the langauge and those who are interested in the language.

## This reference is provisional [↵](#introduction)

This is not the final specification yet, as the langauge is still a work in progress.
The contents of the reference is still provisional and is subject to change at any time.
This means the syntax, languages rules, core and standard libary, compiler infrastructure, package manager/build tool, and other aspect of the design that have not been decided on yet.
This therefore will contain gaps for parts that have not been decided on yet.
There may also be unclear language within this document that still needs to be refined during the development process.

In addition, the current name 'Minoa' is a work in progress (W.I.P.) name and may also still change in the future.

## Conventions

To keep the reference consistent, it like many other techincal books, has a certain set of conventions it follows:

- Word written in _italics_ are used to refer to specific terms within the language, these may in addition also point to a given section which explains them more in detail.
- Notes are used to supply additional info to a section, whenever it does not neccesarily fit within the main text.
> _Note_: This is an example of a note

- Example blocks are used to demonstrate certain features of the langauge as code, with optional comments.
  These may contain hidden lines of code that is not directly relavent to its use, but are needed to make the example compile correctly (this is not yet implemented).

> Example:
> This is a comment
> ```
> // Todo: insert something like a basic hello world
> ```

- Warnings are used to specify possible dangers or unsound behavior which may be confusing
> _Warning_: This is an example of a warning

- Code snippets may define either minoa code, or the grammar of a specific piece of code.
  In case these contain code, these can be run directly or copied to the playground (this is not yet implemented)

```
// Example code block
```

- Implementation notes are used to define features a compiler implementation may support, or how it may deviate from the reference.
> _Implementation note_: This is an example of an implementation note

- Tooling notes are used to indicates useful features that tooling may provide
> _Tooling_: THis is an example of a tooling note

- Reasoning notes allow for additional info to be provided why a certain decision in the reference was chosen.
> _Reasoning_: This is an example of a reasoning note

- Todos are used to indicate that part of the reference is not finished, and defines what changes still need to be made to them.
> _Todo_: This is an example of a todo