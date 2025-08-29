# Attributes
```
<attribute> := '@' [ '!' ] <simple-path> [ '(' <attrib-meta> { ',' <attrib-meta> } [ ',' ] ')' ]
<attrib-meta> := <name>
               | <name> '=' <expr>
               | <name> '(' <attrib-meta> { ',' <attrib-meta> } [ ',' ] ')'
```

An attribute is general metadata that is given to the compiler, the resulting action depends on the attribute itself.
There are 2 types of attributes:
- module attributes starting with `@!`
- normal attributes starting with `@`

The difference between these attributes, is that the first one defined an attribute that is applied to the module it is in (or on the library if the file is a root module),
while the second applies to the item following it.

Expression may be used inside of attributes, but they cannot start using a name.

The following elements can have a attribute applied to them:
- All items
- Most statements
- Block expressions
- Enum variants
- Struct fields
- Match arms
- Function, function pointer, and closure paramters

> _TODO_: Explain how @attr(sub = "") and @attr(sub("")) are interchangable

## Built-in attributes [↵](#attributes)

Built-in attributes are attributes that the compiler can use to change its behavior.

### Meta attributes [↵](#built-in-attributes-)

#### `meta_order` [↵](#derive-attributes-)

The `meta_order` attribute is used to wrap [meta attributes] and control their order of execution.

The attribute contain the attribute to wrap, followed by a `before` and/or `after` argument with the names of the attribute that it should run before or after.

For example, `@meta_order(foo, before(bar), after(baz, quux))`, means that the `foo` attribute should run before `bar` is run, but after both `baz` and `quux`.

### Module attributes [↵](#built-in-attributes-)

These are module specific attributes

#### `path` [↵](#1717-module-attributes-)

The `path` attribute defines a path a module uses, as defined in [module path attribute section]

### Documentation comments [↵](#built-in-attributes-)

#### `doc` [↵](#documentation-comments)

The `doc` comment specifies a pseudo-attribute that represent [doc comments](./lexical-structure/comments.md#doc-attribute-).

## Tool attributes [↵](#attributes)

Tool attributes allow for external tools to supply its own attributes, with their own namespace

## User-defined attributes [↵](#attributes)

User-defined attributes are done using [meta attributes].

In addition, result builders can also be used as the name in these attributes.




[meta attributes]:                         ./metaprogramming.md#meta-attributes-
[module path attribute section]:           ./items/modules.md#path-attribute-
[doc comments]:                            ./lexical-structure/comments.md#doc-attribute-