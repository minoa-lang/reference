# Diagnostic

Diagnostic attributes are a set of attributes which are used to control and generate diagnostic messages during compilation.
Allows for developers to add additional ergonimics to the resulting diagnostic output.

## Lint check attributes [↵](#diagnostic)

Lint check attributes provide control over which lints to show or ignore, resulting in the compiler only checking for a limited set of lints.
These lints are generally meant to indicate potentially undesirable code pattern to the developer, such as unreachable or unused code.

These are a collection of 5 attribute, each controlling how a given lint should be handled:
1. `allow(rule)`: allows the given set of patterns, resulting in the compiler ignoring these lints
2. `expect(rule)`: expects that a given lint would normally be emitted for the given set of patterns, resulting in a `expected_lint` lint, if these lints are not triggered
3. `warn(rule)`: produces a warning when the given set of patterns is not followed, but still allowing code to successfully compile
4. `deny(rule)`: produces an error when the given set of patterns is not followoed, preventing the code to compile
5. `forbid(rule)`: similar to `deny`, but will, at a library level, prevent the rule from being given a lower lint level afterwards

The above lints are also order in terms of their lint level, indicating their severity.

> _Example_
> ```
> pub mod API {
> 
>     // allows the function to have missing docs, even though it's publically available
>     @allow(missing_docs)
>     pub fn undocumented1() {}
> 
>     // will produce a warning that the function has missing docs, but will still allow the code to successfully compile
>     @warn(missing_docs)
>     pub fn undocumented2() {}
> 
>     // will produce an error that the function has missing docs, and will prevent the code from compiling
>     @deny(missing_docs)
>     pub fn undocumented3() {}
> }
> ```

Each lint level may be overriden elsewhere within code, as long as it does not try to change a `forbid`den lint (changes to `deny` are allowed, but ignored).
In this case, it will override any previous lint, meaning any lint in a scope outside of the current one.

> _Example_
> ```
> @warn(missing_docs)
> pub mod top_level {
> 
>     // will result in a waring
>     pub fn undocumented1() {}
> 
>     @allow(missing_docs)
>     pub mod bottom_level {
> 
>         // the missing docs will be ignored
>         fn undocumented2() {}
> 
>         // will result in an error
>         @deny(missing_docs)
>         fn undocumented3() {}
>     }
> }
> ```

> _Example_
> ```
> @!forbid(missing_docs)
> 
> // allows to change to `deny`, but will be ignored
> @deny(missing_docs)
> pub fn undocumented1() {}
> 
> // error: cannot change the lint level from a `forbid` lint level
> @allow(missing_docs)
> pub fn undocumented1() {}
> ```

If multiple lint levels for any given are defined within the same scope, it will result in an error.

> _Example_
> ```
> // error: only a single lint level for any rule can be set for each item
> @allow(missing_docs)
> @warn(missing_docs)
> pub fn undocumented() {}
> ```

### Lint reasons [↵](#lint-check-attributes-)

Each lint check attribute may additionally provide a `reason` why a specific lint level was chosen in case it will result in the lint being triggered.
This reason will be added to the output for the given lint

A lint reason may be added to `allow`, but this will be ignored, unless the compiler is told to output any `allow` lints.

> _Example_
> ```
> @!deny(missing_docs, reason = "It is policy to require all public items to be documented")
> 
> // will output an error, with the reason why
> pub fn undocumented() {}
> ```

### Lint groups [↵](#lint-check-attributes-)

To prevent lints from becoming too verbose, different lints are combined in a lint group, allowing all included lints to have their lint level updated together and use a unified reason.

> _Example_
> ```
> // allows all 'unused' lints
> @!allow(unused)
> 
> // The function will not cause a lint, even though it is used, as we allowed it above.
> // However, the unused variable will produce a lint, as we turned warning on for this specific sub-lint
> @warn(unused_variable)
> fn example() {
>     a := 1;
> }
> ```

The compiler provides a set of [pre-defined lint groups], which are always available and cannot be changed.
Additionally, it provides a special `warn` group, which includes all lints which are currently set to a level of `warn`.
This allows a convenient way to enable a "warning as error" mode by denying the group.

> _Example_
> ```
> // overrides the lint level for all current and future warning lints to error
> @!deny(warn)
> 
> // Would normally produce a warning, but the above deny will result in this producing an error
> @warn(unsafe_code)
> fn fn_with_unsafe(ptr: ^i32) -> i32 {
>     unsafe ^ptr
> }
> ```

It is however possible for both tools and developers to add additional groups, this can be done in the following ways:
- tools may provide these groups through:
  - their compiler plugin, during plugin registration
  - a configuration file which is provided to the compiler
- developers can add their own lint groups using:
  - configuration options in package or module specific configurations
  - a separate configuration file with lint groups

### Tool lints [↵](#lint-check-attributes-)

Unlike compiler provided lints, tool provided lints are located in their own overarching lint namespaces, requiring tool lints to be prefixed with the linter's name, i.e. `linter.lint_rule`.
This allows multiple linters to provide lints with the same name without needing to worry about any conflicts.

These also introduce an additional lint group, which includes all lints provided by the linter, which can be used by just providing the linters name, i.e. `linter`.

## `available` & `unavailable` [↵](#diagnostic)

The `available` and `unavailable` attributes allow an item to indicate under which conditions they can be used.
As their names implies, `unavailable` is the opposite of `available`, indicating when it cannot be used.

This attribute can define a set of features to which the compiled target needs to adhere to be able used this function.
This information will also be used to convey error messages whenever the function is used, but is not available.

The common metadata for these attributes contains system flags which the feature needs to have, or should not have in case of `unavailable`.

Currently, the following features are available:

feature      | configuration options
-------------|-----------------------
`arch`       | [`target_arch`]
`arch_feats` | [`target_features`]
`os`         | [`target_os`]


The `available` attribute may additionally indicate an informative version indicating from which version of the library the item was available.

> _Example_
> ```
> @available(version = 1.0, arch = .x86)
> fn run_on_some_systems() {}
> 
> @unavailable(os = .macos)
> fn run_on_almost_everything() {}
> ```

# `deprecated` [↵](#diagnostic)

The `deprecated` attribute allows items to mark that they will be removed in some future version, and produce a warning or error when used.

It may be applied in the following forms:
- `deprecated`: issues a generic message
- `deprecated("message")`: issue a deprecation message including the provided message
- `deprecated(...)`: provides additional information to improve the handing of the deprecation
  - `msg`: the main deprecation message, this is generally a short version of why the item is deprecated
  - `note`: additional notes for the deprecated item, can be used to specify alternative, or provide additional information about the deprecation
  - `since`: defines a version of the library since when the item is deprecated
  - `renamed`: Indicates an items which should be used instead, generally providing a compatible set of arguments.
               This allows tools to automatically fix any occurances of this function
  - `error_since`: defines the initial version from which point the deprecation will become an error instead of a warning
  - `remove_in`: defines the future version in which the item will be removed.
  
  Any version provided must be ordered relative to each other in the following way: `since` <= `error_since` <= `remove_in`

## `noasync` [↵](#diagnostic)

The `noasync` attribute marks a that a given function cannot be used in an `async` context.
Additionaly, a message can be provided with additional information on why it is not allowed.

Since an `async` function has no guarantee on which thread they will run, or if they will even complete on a single thread.
It can therefore not be guaranteed that things such as [thread local storage] or locks will not cause any issues when transfered to a different thread.

> _Example_
> ```
> @noasync("Uses thread local storage and locks")
> fn tls_and_locks() { ... }
> 
> async fn foo() {
>     // cannot use `tls_and_locks` in an async context: Uses thread local storage and locks
>     tls_and_locks();
> }
> ```

## `must_use` [↵](#diagnostic)

The `must_use` attribute indicates that a given value must be used.
It may be provided with a message, providing a reason why it must be used, and what effect could happen when it is not used.

How exactly the attribute will act, depends on what it is applied.
The following usecases are supported:
- when applied on any item that produces a type, i.e. [structs] (including [tuple structs]), [union], [enums], or [bitfields], any expression producing its type must use the value.

> _Example_
> ```
> @must_use
> struct MustUse {
>     // ...
> 
>     init fn() -> Self {
>         Self { ... }
>     }
> }
> 
> // error: a return value of type `MustUse` must be used
> MustUse();
> ```

- when applied on a [function] which returns a value, any call to the function must use the value.

  > _Example_
  > ```
  > @must_use
  > fn rand_int() -> i32 { 4 }
  > 
  > // error: the value returned by `rand_int` must be used
  > rand_int();
  > 
  > ```

- when applied to a [trait], any call to a function which produces an `impl Trait` or a value containing`dyn Trait` must use the value.

  > _Example_
  > ```
  > @must_use
  > trait Foo;
  > 
  > fn gen_foo() -> impl Foo {
  >     ...
  > }
  > 
  > // error: a return value of `impl Foo` must be used
  > gen_foo();
  > 
  > 
  > fn gen_dyn_foo() -> DynWrapper[dyn Foo] {
  >     ...
  > }
  > 
  > // error: a return value containing a `dyn Foo` must be used
  > gen_dyn_foo();
  > ```

- when appplied to a [trait function or method], any call to an implemenation must use the value

  > _Example_
  > ```
  > trait Bar {
  >     @must_use
  >     fn baz() -> i32;
  > }
  > 
  > struct Foo {
  >     extend impl as Bar {
  >         fn baz() -> i32 { 43 }
  >     }
  > }
  > 
  > // error: the value returned by an implemenation of `Bar.baz` must be used
  > Foo.baz();
  > ```


[`target_arch`]:            ../configuration-options.md#target_arch-
[`target_feature`]:         ../configuration-options.md#target_feature-
[`target_os`]:              ../configuration-options.md#target_os-
[function]:                 ../items/functions.md
[thread local storage]:     ../items/statics.md#thread-local-starage-
[trait]:                    ../items/traits.md
[trait function or method]: ../items/functions.md#trait-functions--methods-
[bitfields]:                ../type-system/types/composite-types/bitfield-types.md
[enums]:                    ../type-system/types/composite-types/enum-types.md
[structs]:                  ../type-system/types/composite-types/struct-types.md
[tuple structs]:            ../type-system/types/composite-types/tuple-struct-types.md
[unions]:                   ../type-system/types/composite-types/union-types.md

[pre-defined lint groups]:  #lint-groups- "Todo: link to docs"