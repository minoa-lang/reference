# Meta utilities

To aid with the creation of meta-function, some additional language-level meta-utilities are provided.

## Meta blocks [↵](#meta-utilities)
```
<meta-block> := '#' '{' ? any sequence of tokens, representing the produced fragment ? '}'
```

A meta-block is synthetic sugar to aid with the construction of fragments.
It takes the representation inside of the block, and converts it to a supported type.

A block may map to the following meta-functions, which will additionally result the relevant types:

meta-function      | return type
-------------------|-----------------------
[`#tokenize`]      | `meta.TokenStream`
[`#synthesize`]    | `meta.Ast` or `meta.fragment.*`

Which meta-function to use is inferred based on the expected type.

### Custom meta block variants [↵](#meta-blocks-)

It is possible to provide a custom variant of a meta-block.

This is done by creating a meta-function with the following signature, and marked using the [`@meta_block`] attribute:
```
@meta_block
meta fn <name>(tokens: ast.TokenStream) -> T { ... }
```
where the return type `T`, similarly to the provided variants, will be determined based on the expected type of the meta-block.

## Utility meta functions [↵](#meta-utilities)

> _Note_: This section will be moved into the documentation

### `#tokenize` & `#synthesize` [↵](#utility-meta-functions-)

This group of 2 meta-function have a common use, i.e. they convert in incoming sequence of tokens into a (partially) parsed result.
The 2 variants do the following:
- [`#tokenize`]: takes the tokens passed to it, and return it. Has a return value of `meta.TokenStream`
- [`#synthesize`]: parsed the tokens into a valid AST or fragment, based on the expected return type

In addition to just converting the provided code to tokens, it also allows the capturing of values and directly adding them into the code.

This is done in a similar way to how variables are defined in a meta-pattern, i.e. `$name`, where `name` is the name of the value to insert.
It will automatically convert the value to tokens which correspond to the code the value represents.

It is possible to use expression directly within the code representation by wrapping it in `$()`.
The result of the expression will then be added within the resulting tokens.

In addition, any type which can be dereferenced into a slice or an optional type may be inserted, together with tokens that are associated with each iteration, or for a valid value respectively.
This is done with by wrapping the value within `${}`, and putting either `*` for slices, or `?` for optional values at the end.

_Example_
```
// Convert a list of 4 tokens, with the value of `bar` imbedded into a sequence of tokens
#tokenize {
    foo($bar);
};

// Similar to above, but this results in a parsed expression
expr: meta.fragment.Expression = #synthesize {
    foo($bar);
};
```

### `#parse_pattern` [↵](#utility-meta-functions-)

[`#parse_pattern`] allows for a `meta.TokenStream` to be converted to a `meta.FragmentStream`, based on a [meta-pattern] provided.

> _Example_
> ```
> #parse_pattern(toks, $iden => $expr);
> ```
> this will parse the tokenstream into a fragment stream consisting of a fragment, a token, and an expression.


### `#match_pattern` [↵](#utility-meta-functions-)

[`#match_pattern`] allows for a `meta.TokenStream` or `meta.FragmentStream` to be matched against multiple patterns, and select an arm to execute based on whether it matches an arm or not.

This will match to the first arm for which the tokenstream matches the pattern.
If no matching pattern is found, and no wildcard pattern is provided, it will result in an error.

_Example_
```
#match_pattern { toks,
    $val:lit => val as i32,
    $_ => 0,
}
```

### `#invoked_repr` [↵](#utility-meta-functions-)

[`#invoked_repr`] allows the meta-function to retrieve the source representation of a given argument that was passed to the meta-function.
This allows for the meta-function to, in addition to the value, also retreive the representation of the value passed.

This can only be called on the parameters of the meta-function, any other values will result in an error.

> _Example_
> ```
> #calc_stringify(1 + 2)
> ```
> with the following implementation
> ```
> meta fn calc_strinify(value: i32) -> (i32, &str) {
>     // retreive the fragment representing the value used in the meta-invocation
>     val_repr = #invoked_repr(value);
>     (value, val_repr.as_str())
> }
> ```
> this will result in a value of `(3, "1 + 2")`


[`@meta_block`]:    ../attributes.md "Todo: Fix up link"
[meta-pattern]:     ../metaprogramming.md#meta-patterns-

[`#tokenize`]:      #tokenize--synthesize- "Todo: link to docs"
[`#synthesize`]:    #tokenize--synthesize- "Todo: link to docs"
[`#parse_pattern`]: #parse_pattern-"Todo: link to docs"
[`#match_pattern`]: #match_pattern- "Todo: link to docs"
[`#invoked_repr`]:  #invoked_repr- "Todo: link to docs"