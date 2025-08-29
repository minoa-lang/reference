# Diagnostic

These attributes are used for controlling or generating diagnostic messages during compilation

# `lint` attributes [↵](#diagnostic-attributes)

Linting attributes allows linters to check for potentially undesirable code patterns, such as unreachable code or omitted documentation.

The following lints attributes are supported:
- `allow(rule)`: overrides checks for `rule` and allows them to be treated as valid, so they are ignored.
- `warn(rule)`: Generates a warning whenever an occurance of `rule` is found, but continues compilation
- `deny(rule)`: Generates an error whenever an occurance of `rule` is found, and terminates compilation
- `forbid(rule)`: Similar to `deny`, but must be at a library level and forbids changing the lint level afterwards

The `rule`s used for these lint checks can be one of the standard compiler lints, or additional linter-specific rules.

Lint attributes are allowed to override the level specified by a previously define lint attribute, as lolng as the level does not try to change a forbidden lint.
Previous attributes are attributes defined in a higher level in the module hierarchy, or those passed directly to the compiler

## Lint groups [↵](#lint-attributes-)

Lint attributes may be combined within lint groups, these have distint names and simultaniously set the lint level for all underlying attributes.
Lint groups can have their individual lint rules overriden by subsequent lint groups.

## Tool lints [↵](#lint-attributes-)

A tool lints are scopes lint rules for certain tools.

Tool lints only get checked when their associated tools are active.
If a tool lint is encountered, but its tools is not active, they will be ignored

# `available` & `unavailable` [↵](#diagnostic-attributes)

The `available` attribute defines since what minimum version of the library an item is available.
The `unavailable` attribute is the oposite of `available` and specifiers for which systems the feature is not available.

In addition, it may also define on which systems it is available and which system flags needs to be supported.

The attribute contains a version number, in addition to zero or more of the following sub-attributes and their respective configuration options:

sub-attribute | configuration option
--------------|----------------------
`arch`        | [`target_arch`](./configuration-options.md#target_arch-)
`arch_feats`  | [`target_features`](./configuration-options.md#target_feature-)
`os`          | [`target_os`](./configuration-options.md#target_os-)

# `deprecated` [↵](#diagnostic-attributes)

The `deprecated` attributes allows items to marked as deprecated and will generate a warning on any use of it.

The `deprecated` attribute can be defined in multiple ways:
- `deprecated`: issues a generic message
- `deprecated("message")`: includes the given string in the deprecation message
- `deprecated(...)`: includes the given attributes in the deprecation message
    - `msg`: The main message
    - `note`: Additional notes for the deprecated item, can be used for to specify alternatives, or additional info why it was deprecated
    - `since`: Defines the semantic version of the library in which this item was deprecated.
    - `renamed`: Used to indicate which function should now be used, if it takes the same arguments. Tooling can use this to automatically fix any occurances of the function.

# `obsolete` [↵](#diagnostic-attributes)

The `obsolete` attribute is similar to `deprecated`, but will instead of resulting in a warning, an error will be generated.

Likd `deprecated`, the `obsolete` attribute can be defined in the following ways:
- `obsolete`: issues a generic message
- `obsolete("message")`: includes the given string in the deprecation message
- `obsolete(...)`: includes the given attributes in the deprecation message
    - `msg`: The main message
    - `note`: Additional notes for the obsolete item, can be used for to specify alternatives, or additional info why it was obsolete
    - `since`: Defines the semantic version of the library in which this item was obsolete.
    - `renamed`: Used to indicate which function should now be used, if it takes the same arguments. Tooling can use this to automatically fix any occurances of the function.

# `noasync` [↵](#diagnostic-attributes)

The `noasync` attribute notifies that a given function cannot be used inside of an async function.
The attribute can provide a message that explains why it cannot be used.

`async` function have no guarantee on which thread they will run, or that they will even complete on a single thread.
Therefore things like thread-local storage and locks can cause issues when transfered to a different thread.

# `must_use` [↵](#diagnostic-attributes)

The `must_use` attribute will issue an warning or error (depending on the current lint level) when the resulting item is not used.
They can be defined on user-defined types and any kind of funtion.

When applied to a user-defined type, any return of a value of this type will result a message.
When applied to a function, if the return value of that function is not used, it will result in a message.

The `must_use` can return a generic message, or can be supplied with a message (`must_use("reason")`), which will print out the reason why the value must be used.

# `diagnostics` [↵](#diagnostic-attributes)

The `diagnostics` attribute is a namespace of attributes that can affect compile time error reporting.
The hints provided by these attributes are not guaranteed to be used.
Unknown attributes in this namespace are accepted, though they may emit warnings for unsused attributes.

> _Todo_: Add diagnostics sub-attribs

[`target_arch`]:     ../configuration-options.md#target_arch-
[`target_features`]: ../configuration-options.md#target_feature-
[`target_os`]:       ../configuration-options.md#target_os-