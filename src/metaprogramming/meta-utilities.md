
# Meta utilities

To help with the creation of meta-functions, the `meta` library includes additional meta-functions to help writing of them.

## `#tokenize`, `partial_synth` & `#synthesize` [↵](#meta-utilities)

These 3 meta-functions are all used to generate a useable representation from raw tokens and values provided to them.
While they all take the same data, their outputs are slightly different
- `#tokenize` will convert the data into a token stream
- `#partial_synth` will convert the data into a fragment stream, where directly written symbols will be passed as tokens, and included values will use their ast fragment representation
- `#synthesize` will convert the data into an AST subtree.

In addition to just converting the provided code to tokens, it also allows the capturing of values and directly adding them into the code.

This can be done in a similar way to how pattern meta values are defined, i.e. `$name`, where `name` is a variable declared within the same scope.
It will automatically interpret the provided value and convert it into the corresponding code.
To use an expressions directly within these functions, the expression can be wrapped in `$(...)`, where the result of the expression will be used.
An expression can also be inserted as a literal value, this is done by prefixing the expressions with `literal:`, i.e. `$(literal: ...)`.

In addition, any type that can be dereferenced into a slice or an optional type may be inserted as their individual elements.
This can be done by surrounding the code that needs to be expended using `${}`, and either `*` for slices and `?` for optional types.

These meta-function can also output additional metadata, telling the compiler which variable created by the meta-function should be visible in the scope they are used in.T
This can be done by adding `output <names> =>` before the code that needs to be generated, where `<names>` is replaced with a comma separated list of the names to 'export' from the meta-function's code.

For example
```
// Convert to a list of 4 tokens: 'foo', '(', ')', the tokens that represent `bar` , and ';'
#tokenize {
    foo($bar);
}

// Converts to a list of 2 tokens: 'foo' and '(', then followed by an ast fragments, and then followed by the 2 tokens ')' and ';'
#partial_synth {
    foo($bar);
}

// Convert to an AST function call node
#synthesize {
    foo($bar);
}
```

> _Note_: the output of `#partial` can not be directly returned from the function, but can be used to pass onto other meta-functions that take in meta-patterns

#### Meta blocks [↵](#tokenize-partial_synth--synthesize-)

In addition, the above 2 meta-function also support some syntactic sugar to make writing them simpler.
Either can be replaced with a so-called metablock, which convert a given piece to code to either a set of tokens or a parse AST subtree, depending on the type expected.

This means that `#tokenize { ... }` or `#synthesize { ... }`, can simple be written as `#{ ... }`.

## `#pattern_parse` [↵](#meta-utilities)

The `#pattern_parse` meta-function allows for a slice of tokens to be parse according to a provided pattern.

The possible patterns are defined in the following [section](../metaprogramming.md#133-meta-patterns-).

For example:
```
fn meta(tokens: []meta.Token) -> ... {
    #meta.pattern_parse { tokens => $name:name };
    // `name` is available here and has the type `meta.ast.Name`
}
```

## `#pattern_match` [↵](#meta-utilities)

The `#pattern_match` meta-function is similar to `#pattern_parse`, but allows multiple meta patterns to be provided and will choose the first matching one.

The match will use the first pattern that matches the tokens that are passed to the meta-function.
If no matching pattern is found, and no wild-card is provided, it will generate a compiler error and return from the meta function.

A pattern within the pattern match must be surrounded by parentheses.
In addition, a `_` can be use as a wildcard pattern.

For example:
```
fn meta(tokens: []meta.Token) -> ... {
    #meta.pattern_match {
        tokens,
        ($name:name) => {

        },
        _ => {

        }
    }
}
```

## `#invoke_repr` [↵](#meta-utilities)

The `#invoke_repr` meta-function allows the program to get the token or AST representation of the value passed to meta-function invocation.
This allows the meta-function to use the representation, in addition to the computed value, of an argument.

`#invoke_repr` can only be called on the parameters of the meta-function.

For example, when we invoke the following macro:
```
#stringify(1 + 1)
```
the `#invoke_repr` allows use to get the actual expression passed:
```
meta fn stringify(val: i32) -> (i32, &str) {
    // Get the representation that was passed to the invocation
    val_repr := #invoke_repr(val);
    (val, val_repr.to_str())
}
```
This would return us the value `(2, "1 + 1")`

## `#library` & `#package` [↵](#meta-utilities)

The `#library` and `#package` meta-functions are special macros that can be used to contruct path refering to the library and package in which the meta-function is defined respectively.
