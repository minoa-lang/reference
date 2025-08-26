# Metaprogramming 
```
<meta-decl>       := 'meta' 'fn' <name> [ <deduced-params> ] [ <meta-signature> ] '->' <meta-return> <block>
                   | 'meta' 'fn' <name> <meta-pattern-match-body>
<meta-signature>  := <meta-pattern-parse-signature>
                   | '(' <name> ':' ? Token slice or AST type ? ')'
                   | '()'
<meta-return>     := 'type'
                   | ? Tokens or AST type ?

<meta-expr>       := '#' <name> [ <meta-invocation> ]
<meta-invocation> := '(' <tokens> ')'
                   | '{' <tokens> '}'
                   | '[' <tokens> ']'
```

Meta-functions are special function-like items which allow for the compile-time generation of code.
Like regular functions, meta-function can be passed induced arguments and type arguments.
But unlike regular functions, meta-functions can return type created within the function.

In addition to the data passed to the meta-function, the meta-function can also use the provided info passed in the implicit context.

Meta-function can be defined within the library they are declared it, and can use any function available within that library.
However, a major restriction is that the code it relies on may **not** have any circular reliance on the current meta-function.

> _Note_: Meta-function must be defined directly within a module and may not be defined inside of any other item.

> _Todo_: Is the API correct for this entire section?

## Meta variable [↵](#metaprogramming)

A meta variable takes in no arguments and just returns some generated code.

Meta variables can both be called with or without subsequent meta invocation (i.e. delimiters).

In addition, if a meta-function takes in parameters, but all have default value defined, the function can be called as a meta-variable and will be provided with the default values.

For example:
```
meta fn meta_var() {
    ...
}
```
Can be called as either `#meta_var()` or `#meta_var`

## Regular meta functions [↵](#metaprogramming)

A regular meta-function acts like a normal function, taking in data directly from the compiler.

Although a meta-function may take any kind of input, there are 3 special signatures that have a different effect.

The special signatures are:
- `(tokens: meta.TokenStream)`: Provides the raw tokens given by the meta-function invocation
- `(partial_ast: meta.PartialAst)`: Provides a combination of AST fragments and tokens
- `(ast: meta.Ast)`: Provides a pre-parsed AST of the tokens given by the meta-function invocation

> _Note_: only the types need to match for the special signatures, the names don't matter

For example
```
use minoa:meta;

meta fn foo(tokens: meta.TokenStream) -> ... {
    ...
}

meta fn bar(ast: meta.Ast) -> ... {
    ...
}
```

## Meta patterns [↵](#metaprogramming)
```
<meta-pattern>           := { <token> | <meta-pattern-variable> }+
<meta-pattern-variable>  := '$' <name> : <meta-token-type>
                         | '$' '(' <meta-pattern-signature> ')' <macro-rep>
<meta-pattern-type>      := 'item'
                         | 'block'
                         | 'stmt'
                         | 'pat'
                         | 'expr'
                         | 'ty'
                         | 'iden'
                         | 'path'
                         | 'meta'
                         | 'vis'
                         | 'lit'
                         | 'toks'
<meta-pattern-rep>       := '?' | '*' | '+'
```

Meta patterns can be used to extract AST fragments form a given set of token inputs.T
They can be thought of as syntactic sugar utilizing some of the builtin meta-functions.

Meta-functions taking in meta patterns will have a signature that either takes in a `meta.TokenStream` or a `meta.FragmentStream`.


A meta function allows the function to take in a set of pre-parsed AST elements.
In addtion, `toks` can be used to supply a delimited set of raw tokens within the pattern (including the open and closing delimiters).

Each pattern type is syntactic sugar for the corresponding AST node, each corresponding to the following type, assuming `use minoa:meta;`:

pattern kind | fragment type         | corresponding language 'item'
-------------|-----------------------|-------------------------------
`item`       | `meta.ast.Item`       | an [item](./items.md)
`block`      | `meta.ast.Block`      | a [block](./expressions/block-expressions.md)
`stmt`       | `meta.ast.Stmt`       | a [statement](./statements.md)
`pat`        | `meta.ast.Pattern`    | a [pattern](./patterns.md)
`expr`       | `meta.ast.Expr`       | an [expression](./expressions.md)
`ty`         | `meta.ast.Type`       | a [type](./type-system/types.md)
`name`       | `meta.ast.Identifier` | a [name](./lexical-structure/names.md)
`path`       | `meta.ast.Path`       | a [path](./identifiers-paths.md#paths-)
`meta`       | `meta.ast.Meta`       | an [attribute](./attributes.md)
`vis`        | `meta.ast.Vis`        | a [visibility](./visibility.md)
`lit`        | `meta.ast.Literal`    | a [literals](./literals.md)
`toks`       | `meta.TokenStream`    | Any lexical [token](./lexical-structure.md)

> _Note_: The fragments themselves are wrapped in type `meta.Fragment(T)`, where `T` is the fragment type


WHen using a special group pattern, indicated by a sub-pattern surrounded by `${}`, the amount of repetition are decided by the character following it.
This is one of the following
char | meaning
-----|-------------------------
`?`  | zero or one occurance
`*`  | zero or more occurances
`+`  | one or mor occurances

Group pattern are represented using a corresponding `meta.AstList(T)` type.


When matching a pattern, no lookahead is performed.
Meaning that patterns may be ambiguous, in this case, it will result in an error.
An example of an ambigous pattern is the following:
```
$( $i:iden )* $j:iden
```
as it can never reach the final identifiers


Pattern meta-functions are syntactic sugar for a meta-function taking in tokens and applying the `#pattern_parse` meta-function to them.

Meaning that
```
meta fn foo$( $iden:iden ) {
    ...
}
```
can be thought of as
```
meta fn foo(tokens: []meta.Token) {
    #meta.pattern_parse(tokens, $iden:iden);
    ...
}
```

> _Note_: This is syntactic sugar for a meta-function

### Parsed pattern meta-functions [↵](#meta-patterns-)
```
<meta-pattern-parse-signature> := '$' '(' <meta-pattern> ')'
```
A parsed pattern meta-function will automatically parse its input according to a given pattern.

This meta-function can be thought of as syntactic sugar for
```
meta fn name(tokens: meta.FragmentStream) {
    #pattern_parse(tokens, <pattern>)
}
```
Where `<pattern>` represents the pattern defined in the signature

### Matched pattern meta-functions [↵](#meta-patterns-)
```
<meta-pattern-match-body> := '$' '{' { <meta-match-arm> }+ '}'
<meta-match-arm> := '(' <meta-pattern> ')' '=>' <block-expr>
```
A matched pattern meta functions allows a meta-function to change its behavior based on the data passed into it.

The meta-funciton is essentially syntactic sugar for
```
meta fn name(tokens: meta.FragmentStream) {
    #pattern_match { tokens,
        <pattern-0> => { ... },
        <pattern-...> => { ... },
        <pattern-n> => { ... },
    }
}
```
Where `<pattern-...>` represent the patterns defined in the meta body.

## Meta attributes [↵](#metaprogramming)
```
<meta-attribute>          := 'attr' [ '(' <meta-attribute-role> ')' ] 'fn' <name> '(' <meta-attr-signature> ')'
<meta-attribute-role>     := 'derive' '(' <path> { ',' <name> }* ')'
<meta-attr-signature>     := <name> : ? AST type ? [ ',' <name> ':' ? Attribute argument map type ? ] [ ',' ]
                           | <meta-pattern-signature> [ ',' <name> ':' ? Attribute argument map type ? ] [ ',' ]
```

A meta-attribute is a variation on a meta-function and represents an attribute that is applied to a piece of code.

Unlike regular meta-functions, meta-attributes have a strict input signature and return type.
Meta-attributes must take either one of the following as input:
- `meta.Fragment(..)`: This takes in a specific fragment and restrict what the attribute can be applied on
- `meta.Ast`: This takes in any AST sub-tree this attribute is applied on
In additon, a second argument can be passed of type: `meta.AttrArgs`, this contains a collection of key-value pairs of arguments passed to the attribute.

The role of meta-attribute is supplied directly after the `attr` keyword, and is one of the following:
- `full`: consumes the full fragment to which it is attached, allowing the user to use it in any way they like. If the fragment is not returned, the original fragment will not be compiled.\
  This is the default mode of a meta-attribute.
- `peer`: adds code inside of the same scope in which the fragment it is applied to is located. The original fragment is unaffected.
- `member`: adds code within the fragment. The fragment must be an item. The original fragment is unaffected.
- `member_attr`: adds attributes to the any of the members of the item it is applied to. The original fragment is unaffected.
- `accessor`: adds accessors to a field or property. When implemented on a field, it might automatically convert the field into a property, depending on the additional info that is provided.
- `derive(Trait)`: used to add an implementation of the associated trait to the type. This function can be called by either using the meta-attribute directly, or when the trait is given to the [`derive` attribute](#derive-). The original fragment is unaffected.\
  In addition, a derive attribute may also declare a set of helper macros it can use.
  These are defined after the trait within the `derive` role.
  They may take in any amount of additional info.

> _Note_: When a meta-attribute mentions that the original fragment is unaffected, it means that all the original content of the fragment will be retained, it however does get additional code added to it.

Multiple meta-attributes are allowed to share a name, if they have distinct roles.
For example, 2 functions with the same name, but one with a `member_attr` role and the other with a `accessor` role, could exist.
This allows the same name to be used in different contexts, where the different role can be distinguished by its location.

In cases where multiple attributes with the same name would operate on a fragment, both will be ran.

> _Note_: `full` cannot be assigned together with any other role


Some meta-attribute roles require some additional info to be provided:
- `names`: provided the names of the generated fragments. Required for `peer`, `member`, and `accessor`.\
  The value provided to this has to be one of the following, and also defines the name of the generated fragment:
  - `named(...)`: the name will be specific name given to this value
  - `overloaded`: the name will be the same as the fragment its applied on
  - `prefixed(...)`: the name will be that of the fragment, but with the given value as a prefix
  - `suffixed(...)`: the name will be that of the fragment, but with the given value as a suffix
  - `arbitrary`: the name cannot be determined beforehand
- `field_convert`: notifies whether a meta-function might convert a field into a property. Only allowed for `accessor`.

The return type of these attributes depend on the type of meta-attribute being defined:
- `member_attr`: returns an iterator of member names and attribute fragments to be added, i.e. `(String, meta.Fragment(meta.ast.Attribute))`
- `accessor`: returns an iterator of property accessors to be added, i.e. `meta.Fragment(meta.PropertyAccessor)`
- `derive`: return an impl field fragments, i.e. `meta.Fragment(meta.ast.ImplField)`
- otherwise returns an AST sub-tree.

> _Note to LSP developers_: It could be useful to provide the programmer a way of know if a mete-attribute is of kind `full` or `accessor` and let them know code might be changed.

There are also some limitation to where a meta-attribute can be applied:
- `member` and `member_attr`: Only allowed on fragments that may contain members.
- `accessor`: Only allowed of fields and properties.
- `derive(...)`: Only allowed on items

## Meta returns

Meta functions may do not have a specific return value, and may therefore return values, types, tokens, ASTs, etc.

Returning a value can be useful when wanting to take in one representation, and outputting a value that has a different representation of it, without requiring a ton of function calls.
Especially when you want to use functionality which is normally private to the library containing the type of the value being returned.

For example, writing json directly in code and returning a json type:
```
let json_obj = #json {
    "foo" = "5",
    "bar" = {
        "baz" = "true",
        "quux" = [ "a", "b", "c" ]
    }
}
```
This can be used to return a `JsonObject` type, and allows private items of the type to be used, as the value is returned, not the code producing the value.

> _Note_: This may also return a value of type `type`

### Special meta return types

In addition to returning values of a given type, there are special return types with additional meanings, these are the following:
- `meta.TokenStream`: returns a sequence of tokens, which will than be parsed by the compiler within the surrounding code.
- `meta.Fragment(...)`: returns a specific fragment type.
- `meta.Ast`: returns an AST sub-tree.
- `(T, meta.HygieneInfo)`, where `T` is any of the above types: return one of the above types, but additionally provided data with hygiene rules to prevent things like shadowing of variables.

## Meta-aliases
```
<meta-alias> := ( 'meta' | 'attr' [ '(' <meta-attribute-role> ')' ]) 'fn' <name> [ <deduced-parameters> ] <meta-signature> '=' <macro-invocation> ';'
```

Meta aliases allow simple way of re-using macros with different inputs, while still achieving the same output.
They can alias both regular meta-functions and meta-attributes.

## Meta-function execution

Meta-functions are executed from top to bottom, and outer to inner, on the fragments they are attached to.
If 2 meta-functions are in independent context, there is not guarantee to which will be implemented first.

With the exception of a `full` meta-attribute, the generated code will be added at the end of the current code.
While for non-member fragments, this has no impact, this is important to know when

Therefore, the user can generally **not** rely on having info of any code that is generated by another meta-function.
However, meta-attribute can be forced to wait for another meta-attribute to have been evaluated before or after another meta-attribute.
This is done by wrapping the attribute inside of a `@meta_order` attribute

Whenever meta-functions are executed, they are ran in a sandboxed way, they can therefore not:
- access files on the system
- accces any network
- share state between runs
- have any other side-effects

## Meta invocations [↵](#metaprogramming)
```

<meta-invoke> := '#' <simple-path> [ <meta-invocation> ]
<meta-args>   := '(' <tokens> ')'
               | '{' <tokens> '}'
               | '[' <tokens> ']'
```

A meta invocation is used to invoke a meta variable or function.

A meta variable is invoked by just calling the meta variable without any arguments.

Each invocation will be replaced by the code produced by the meta-function.

A meta invocation can be located anywhere in code, but requires the surrounding delimiters to match, as a meta-function may not introduce unmatched delimiters.

A meta-invocation may appear in the following situations:
- [expressions](./expressions.md)
- [statements](./statements.md)
- [patterns](./patterns.md)
- [types](./type-system.md)
- [items](./items.md)

## Meta hygiene

The hygiene of a macro depends on what it returns.

If a macro returns a value, it is _hygienic_, as it does not impact the surrounding code, as not variables are declared that are visible outside of the macro invocation.

If a macro return any of the special types, without any `HygieneInfo`, then they are,  _unhygienic_, meaning that they may introduce new variables of items within code.
These variables can shadow pre-declared variables, whenever this happens, a `meta_shadow` lint will be produced.
By default, this lint is set as `@!warn(meta_shadow)`, and can be disabled using the `@allow(meta_shadow)` attribute.

In addition to any of hte special types, if `Hygiene` info is provided, the meta-functin is _partially hygienic_`.
Only the data specified in the hygiene info will be visible outside of the macro invocation, which may still cause some values to be shadowed.
In this case, the same `meta_shadow` lint will be used.

To ensure that variable names generated within not to conflict with any name that could be passed into the generated code, the `make_unique_name()` function can be used.
This is accessible from the context provided into the meta function.
