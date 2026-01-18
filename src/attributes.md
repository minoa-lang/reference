# Attributes
```
<attribute> := `@` [ '!' ] <simple-path> [ '(' <attribute-metas> ')' ]
```

An attribute allows metadata to be passed in a generalized free-form to any fragment which supports them.

Attributes may define any compiler metadata or [meta attributes] to the fragment, the former of which is commonly (but not always) implemented as the latter behind the scenes.

The following framgnets may have attributes applied to them:
- all [items]
- most [statements]
- [block expressions]
- fields
- [match] arms
- [function], [raw function], and [closure] parameters

Attributes can be applied in 2 ways:
- `@`: this applies the attribute to the fragment to which is it applied
- `@!`: this applies the to the fragment containing it. In case of the [main module], it will apply to the entire library.

## Attribute metadata [↵](#attributes)
```
<attribute-metas>          := <attribute-meta> { ',' <attribute-meta> }*
<attribute-meta>           := <expr>
                            | <simple-path> [ '=' ( <expr> | <version> | <attribute-meta-free-form> ) ]
                            | <simple-path> '(' <attribute-metas> ')'
                            | <version-number>
                            | <attribute-meta-free-form>
<version-number>           := <int-decimal-literal> { '.' <int-decimal-literal> }[1..2] [ '(' <hex-value> ')' ]
<attribute-meta-free-form> := '[ <ext-name> '=' ] { ? token tree ? }
```

Attributes can be supplied with additional metadata, allowing the user to have more control over attributes.

This metadata may be provided in 5 different ways:
- [expressions]:
  
  Any expression provided to an attribute must result in a compile-time constant value, since attribute are applied at compile-time and must therefore know these values.

  > _Example_
  > ```
  > @attr_with_expr(1 + 2, 3.0f32, const_fn())
  > struct Struct;
  > ```

- a path-only value
  
  These are generally used to toggle setting within the attribute, allowing control without requiring a value to be passed explicitly

  > _Example_
  > ```
  > @names_only(a, b.c)
  > struct Foo;
  > ```

- an assigned name

  Allows for an [expressions] or sequence of tokens to be bound to a given name
  
  > _Example_
  > ```
  > @named_args(a = 1, b = true)
  > struct Foo;
  > ```

- a named sub-metadata collection

  Allows nested attribute meta-data to be provided.

  If the inner attribute meta-data were to contain any expression or token sequence directly, it will be passed as if it were directly assigned to the preceding tokens, in cases where this is not represented as a by an `attr_param` parameter

  > _Example_
  > ```
  > @nested(a(b, c = 2), d(e, f))
  > struct Foo;
  > ```

- version, which can consists of a major and minor version, 2 additional values can be added:
  - a patch number at the end
  - an additional [epoch] version at the beginning, which requires a patch number to be parsed correctly

  Additionally, a build number can be added in parentheses at the end, this is represented by a hexadecimal sequence, without the preceeding `0x`.

  > _Example_
  > ```
  > @version_2(1.2)
  > @version_3(1.2.3)
  > @version_4(1.2.3.5)
  > @version_with_build(1.2.3 (ab7f))
  > fn foo() {}
  > ```

- free-form, allowing for any sequence of tokens to be used.
  
  > _Example_
  > ```
  > // always ensure the following invariant contract
  > @invar(self.w == self.h)
  > struct Square {
  >     w: i32,
  >     h: i32
  > }
  > ```

  > _Implementation_: Since free-form attribute meta may contain any data, they can only be parsed after the compiler has figured out the signature for the attribute.
  >                   However, if the compile can parse this as if it were regular attribute metadata, it will parse it as if it was.
  >                   This is possible by the fact that a pattern may not contain a `,` which is not nested.

> _Note_: A value within the metadata that is written as either `name = value` or `name(value)` have the same meaning

## Builtin attributes [↵](#attributes)

Built-in attribute are attribute provided by the compiler, allowing them to do things such as:
- providing functionality which only can be provided by the compiler
- changing the behavior of generated code

Below are some of the builtin attributes, specifically those who do not fit in any of the sub-categories of the attribute located within their own files.

### `unsafe` [↵](#built-in-attributes-)

The `unsafe` attribute allows for any unsafe to be applied within it, this is used to clearly mark that adherence to any guarantees provided by the attribute are entirely dependent on the developer ensuring that these are followed, as the compiler cannot guarantee them by itself.

The following built-in attribute are unsafe:
- [`link`]
- [`no_mangle`]

### `path` [↵](#built-in-attributes-)

The `path` attribute is a [file module]-only attribute.

The paths must be provided as [string literals].

For more info, see the [module path attribute section]

### `doc` [↵](#built-in-attributes-)

The `doc` attribute represent a pseudo-attribute which has the same meaning as [doc comments].

Doc comments are converted into this attribute.

### Meta-programming attributes [↵](#built-in-attributes-)

These attributes provide additional information to both [meta functions] and [meta attributes], and should not be confused with the latter.

#### `meta_order` [↵](#meta-programming-attributes-)

The `meta_order` attributes allows an explicit dependency to be defined for attribute which are applied to the same fragment.

The attribute can provide both `before` and `after`, each defining the names of other attribute before and after which the attribute should be applied, respecively.

For more info, see [meta evaluation order].

#### `attr_param` [↵](#meta-programming-attributes-)

The `attr_param` attribute allows a struct to be used as a nested parameter for an attribute.

In addition, the attribute also provides the `attr_param_value` helper attribute, which marks the field which will be used when a value is directly passed to the parameter.

For more info, see [meta attribute parameters].

## Tool attributes [↵](#attributes)

Tool attributes are special attribute which can be used by external tools, where each tool will be represented by its own module in the [tool prelude].
This means that the attribute path will exists out of 2 parts, the module for the specific tool to use, and the actual tool attribute, i.e. `@tool.name`.

> _Example_
> ```
> @format.skip
> struct A;
> 
> @foo.bar(100)
> struct B;
> ```

Tools provide the definition of their attribute in 2 ways:
- when the tool is a compiler plugin
- when defined in an external file, passed to the compiler

## User-defined attributes [↵](#attributes)

User defined attributes are provided using by [meta attributes].

> _Example_
> ```
> attr fn foo(ast: meta.Ast) {}
> 
> @foo
> struct Bar;
> ```



[`link`]:                        ./attributes/abi-link-symbol-ffi.md#link-
[`no_mangle`]:                   ./attributes/abi-link-symbol-ffi.md#no_mangle-
[expressions]:                   ./expressions.md
[block expressions]:             ./expressions/block-expressions.md
[closure]:                       ./expressions/closure-expressions.md
[match]:                         ./expressions/match-expressions.md
[items]:                         ./items.md
[function]:                      ./items/functions.md
[module]:                        ./items/modules.md
[module path attribute section]: ./items/modules.md#path-attribute-
[doc comments]:                  ./lexical-structure/comments.md#doc-attribute-
[string literals]:               ./literals.md#string-literals-
[meta functions]:                ./metaprogramming.md
[meta attribute parameters]:     ./metaprogramming.md#attribute-parameters-
[meta attributes]:               ./metaprogramming.md#meta-attributes-
[meta evaluation order]:         ./metaprogramming.md#evaluation-order-
[main module]:                   ./package-structure.md#main-module-
[tool prelude]:                  ./preludes.md#tool-prelude-
[statements]:                    ./statements.md
[enum variants]:                 ./type-system/types/composite-types/enum-types.md#variants-
[raw function]:                  ./type-system/types/function-like-types/raw-function-types.md

[epoch]:                         https://antfu.me/posts/epoch-semver