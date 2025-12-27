# Metaprogramming 
```
<meta-decl> := { <attributes> }* 'meta' [ '(' <meta-hygiene-info> ')' ] 'fn' <name> [ <deduced-parameters> ] [ <mata-signature> ] <block>
```

A meta-function is a special function-like item which can work directly on sections of code and generated new code based on it.
Similar to regular functions, metafunctions may take in both deduced and regular parameters, although the latter work differently.

Unlike regular functions, meta-functions are also allowed to return a value of the type `type`.

In addition, each meta-function has access to a special instance of the [implicit context], which can be used during code generation.

Meta-functions can be defined within the same library they are used in, meaning they do not need to come from an external library to be able to be used.
The sole restrictions is that any code the meta function relies on, may not circularly rely on the meta-function being written.

> _Note_: In the context of a metaprogramming, a _fragment_ represents any piece of code which can be parsed as a single element as a whole

> _Note_: Meta-function must be defined directly within a module and may not be defined inside of any other item, with the exception of meta-method.

> _Example_
> 
> Create a json object using json syntax
> ```
> let json_obj = #json {
>     "foo" = "5",
>     "bar" = {
>         "baz" = "true",
>         "quux" = [ "a", "b", "c" ]
>     }
> };
> ``` 

## Meta signature [↵](#metaprogramming)
```
<meta-signature> := '(' <meta-param> { ',' <meta-param> } [ ',' ] ')' [ '->' <type> ]
                  | <meta-pattern-signature> '->' <type>
                  | <match-meta-patttern-signature>
<meta-param>     := <fn-arg>
                  | <meta-pattern-param>
```

A meta-function's signature defines how the passed code will be interpreted, and what is retuned by the meta-function.
The meta-function is allowed to take in a mix of values that are passed as is, and code which will be provided by a supported representation.

A meta function is not required to return anything, but limits its usefulness to what side-effects can be done by interacting with the `meta` library.

The types that support parsed code being passed is one of the following:
- `meta.TokenStream`: code is passed as a sequence of tokens
- `meta.FragmentStream`: code is passed as a sequence of parsed fragments
- `meta.Ast`: code is passed as a fully parsed sub-AST

These types are also those which can be returned from a meta-function, and which the compiler will evaluate as code, rather than a value.
If the actual value of one of these types should be returned, this should be wrapped by a [1-ary tuple].

> _Example_
> ``` 
> // valid meta-function, but has limited functionality
> meta fn limited_use() {}
> 
> // return a value of type `type`
> meta fn return_type() -> type { ... }
> 
> // returns a tokenstream, which the compiler will parse into the surrounding code
> meta fn parsable_code() -> meta.TokenStream { ... }
> 
> // return the actual tokenstream value, and does not parse it
> meta fn token_stream_value() -> (meta.TokenStream,) { ... }
> ``` 

## Meta patterns [↵](#meta-signature-)
```
<meta-pattern-signature> := '$' '(' <meta-pattern> ')'
<meta-pattern-param>     := <meta-pattern-variable>
                          | '$' '(' <meta-pattern> ')'
<meta-pattern>           := { ? <token> except for '$' ? | <meta-pattern-variable> | '$$' }*
<meta-pattern-variable>  := '$' <name> [ ':' <meta-tok-kind> ]
                          | '$' '(' <meta-pattern> ')' <meta-pattern-sep> <meta-pattern-rep> 
<meta-fragment-kind>     := 'item'
                          | 'block'
                          | 'stmt'
                          | 'expr'
                          | 'pat'
                          | 'ty'
                          | 'iden'
                          | 'path'
                          | 'attr'
                          | 'vis'
                          | 'lit'
                          | 'toks'
                          | 'tt'
                          | 'meta_pat'
<meta-pattern-rep>       := '?' | '*' | '+'
<meta-pattern-sep>       := ? <token> other than '*' or '+' ?
                          | '(' { <token> }* ')'
```

A meta-pattern extracts a sequence of fragments from the provided `meta.TokenStream` or `meta.FragmentStream`, or convert the tokens to another `meta.FragmentStream`.

The meta-pattern also allows the function to directly use any of these values using the pattern's provided variables.
These variables are directly available by their name, as defined within the pattern.

Below is a table of each fragment kind, its mapped type, and the item it refers to:

fragment kind | fragment type                 | corresponding langauge fragment
--------------|-------------------------------|---------------------------------
`item`        | `meta.fragment.Item`          | an [item]
`block`       | `meta.fragment.Block`         | a [block]
`stmt`        | `meta.fragment.Statement`     | a [statement]
`expr`        | `meta.fragment.Expression`    | an [expression]
`pat`         | `meta.fragment.Pattern`       | a [pattern]
`ty`          | `meta.fragment.Type`          | a [type]
`iden`        | `meta.fragment.Identifier`    | an [identifier]
`path`        | `meta.fragment.Path`          | a [path]
`attr`        | `meta.fragment.AttributeData` | an [attribute] or any attribute data
`vis`         | `meta.fragment.Visibility`    | [visibility]
`lit`         | `meta.fragment.Literal`       | a [literals]
`toks`        | `meta.TokenStream`            | any sequence of [lexical token]
`tt`          | `meta.TokenTree`              | same as `toks`, but containing only matching sets of delimiters `()`, `[]` or `{}`, no separate ones
`meta_pat`    | `meta.fragment.MetaPattern`   | a meta-pattern

> _Example_
> ```
> fn pattern$($key:expr => $lhs:expr + $rhs:expr) {}
> 
> #pattern(a => b + c - d + e);
> // this will have the following framents for
> key := a;
> lhs := b;
> rhs := c - d + e;
> ```

If the name of the variable happens to match the name for the fragment kind it represents, its kind can be left out

_Example_
```
// the first variable has a different name compared to its kind, so must explicitly mention it
// the second variable has the same name as its kind, so its kind may be left out.
fn pattern$($name:iden => $expr)
```

Additionally, a subset of the pattern may be defined as repeating for a given amount of repititions.
These repitions are defined by the repetition that is located directly after the `${ ... }` element.

These can either be one of the following simple repetitions:

repetition                                          | meaning
----------------------------------------------------|---------
`?`                                                 | zero or one occurance
`*`                                                 | zero or more occurances
`+`                                                 | one or more occurances

> _Example_
> ```
> fn zero_or_once$($( $name:iden )?);
> 
> #zero_or_once();
> #zero_or_once(a);
> 
> // error: expected either 0 or 1 repetitions of an identifier
> #zero_or_once(a b);
> 
> fn zero_or_more$($( $name:iden )*);
> 
> #zero_or_more();
> #zero_or_more(a);
> #zero_or_more(a b);
> 
> fn one_or_more$($( $name:iden )+);
> 
> // error: expected at least 1 repetitions of an identifier
> #one_or_more();
> 
> #one_or_more(a);
> #one_or_more(a b);
> ```

or a more complex repetition in the form of `[<name>]`, `[<name>:<rep>]`, or `[<name>:<range>]`.
Here, the number of repetitions is stored in a variable of `<name>`, which may then optionally be followed by either:
- a simple repetition, defining a simple bound
- an integer range of allowed repetitions

If the value stored in `<name>` is used in multiple locations in the pattern, all these repetition must repeat the same number of times.

> _Example_
> ```
> fn common_rep$($( | )[N] $name:iden $( | )[N]);
> 
> common_rep(a);
> common_rep(| b |);
> common_rep(||| b |||);
> 
> // mismatch between repetitions in pattern
> common_rep(| b ||);
> 
> fn simple_rep_bound$($( | )[N:+] $name:iden $( | )[N:+]);
> 
> // error: expected at least on repetition of `|`
> simple_rep_bound(a);
> 
> simple_rep_bound(| b |);
> simple_rep_bound(||| b |||);
> 
> 
> fn range_rep_bound$($( | )[N:2..3] $name:iden $( | )[N:2..3]);
> 
> // error: expected at least 2 repetitions of `|`
> range_rep_bound(| b |);
> 
> range_rep_bound(||| b |||);
> 
> // error: expected at most 3 repetitions of `|`
> range_rep_bound(|||| b ||||);
> ```

When using either the `*`, `+`, or complex repetition, a separator may be placed before it, this indicates the sequence used to separate multiple different repetitions.
If not provided, no separator will be used.

If the separator consists out of a single token, it can be placed directly between the pattern and repetition (any token except for `*` or `+`).
Any other separator, i.e. `*`, `+` or a multi-token separator, must be located between `()`.

Since `$` is used of the to introduce special patterns, it can also represent a `$` token directly, this can be done in 2 ways:
- by escaping the `$` with another `$`, i.e. `$$` (this is mainly when used in a larger token)
- by having a `$` with whitespace directly after it, i.e. `$ other_token`

> _Note_: A standalone meta-function that extracts a sequence of fragments from a pattern and an input, is available as [`#parse_pattern`].

> _Note_: Additional restriction on parsing patterns might be added in the future

> _Implementation_: When a meta-function is declared using a meta-pattern signature, it will implicitly take in a single parameter of type `meta.FragmentStream`, but this is not directly accessible

### Meta-match function [↵](#meta-patterns-)
```
<meta-match-signature> := '$' '->' <type> '{' { <meta-match-arm> }* '}'
<meta-match-arm>       := <meta-pattern> '=>' <block-expr>
                        | '$' '_' => <block-expr>
```

A meta-match function is a variant of a pattern meta-function, which allows multiple possible patterns to be provided, each resulting in a different result.

The match does not have to be exhaustive, however, if no arm matches, it will result in an error.

> _Example_
> ```
> meta fn matched$ -> i32 {
>     $val:lit => val as i32,
>     $_ => 0,
> }
> 
> assert(#matched() == 0);
> assert(#matched(2) == 2);
> ```

> _Note_: A standalone meta-functon that can match on meta-patterns, is available as [`#match_pattern`].

### Meta pattern matching [↵](#meta-patterns-)

When matching a pattern, no lookahead is performed.
This means that a pattern may be ambiguous during parsing, in this case, this will result in an error.

> _Example_: Ambiguous pattern
> ```
> $($iden:iden)* $iden:iden
> ```
> 
> This pattern will consume all identifiers within the repetition, only exiting it when it consumes all possible identifiers.
> This will result in the last identifier outside of the repetition to never be able to match a valid fragment.

> _Todo_: In the future, it might be possible to add an explicit lookahead control to the pattern syntax.

## Mutli-stage method-functions [↵](#metaprogramming)

Sometimes a meta-function must be ran in multiple stages to operate correctly.
This is required, as both precedences and operators affect the resulting parsed order of fragments passed into the meta-function.

This is controlled by 2 things:
- a [`@meta_stages`] attribute
- a `stage` value within the context

The following stages are supported:
- `precedence`: the meta-function generates a precedence
- `operator`: the meta-function generates an operator set
- `default`: main pass of the meta-function

> _Example_
> ```
> @meta_stages(precedence, operator, default)
> meta fn multi_stage(ast: meta.Ast) -> meta.Ast {
>     match #ctx.stage {
>         .Precedence => { ... },
>         .Operator => { ... },
>         // .Default
>         _ => { ... },
>     }
> }
> ```

> _Implementation_: When any meta-function with a `precedence` or `operator` stage, the parser will parse the library in stages, only parsing what is necessary to evaluate these stages, only after this, will it parse the rest of the library

## Meta methods [↵](#metaprogramming)
```
<meta-method> := { <attributes> }* 'meta' 'fn' <fn-receiver> <name> [ <deduced-parameters> ] [ <mata-signature> ] <block>
```

If is also possible to create [method]-like meta-functions, i.e. meta-function which are called on a receiver.

Unlike a meta-fuction, and like methods, a meta-method must be defined as an associated item to a type.

> _Example_
> ```
> struct Foo;
> 
> impl Foo {
>     meta fn(&self) meta_method() -> i32 { 1 }
> }
> 
> foo := Foo;
> foo.#meta_method();
> ```

## Meta invocation [↵](#metaprogramming)
```
<meta-invocation>        := '#' <meta-invoke-root> [ '(' <meta-args> ')' ]
                          | '#' <meta-invoke-root> <expr> ';'
                          | '#' <meta-invoke-root> ? any fragment other than 'expr' ?
<meta-invocation-expr>   := '#' <meta-invoke-root> <meta-call-args>
<meta-method-invocation> := <expr> [ '?' ] '.' '#' <ext-name> <meta-call-args>
<meta-invoke-root>       := '#' <ext-name>
                          | '#' <path>
<meta-call-args>         := '(' <meta-args> ')'
                          | '[' <meta-args> ']'
                          | '{' <meta-args> '}'
                          | <expr>
<meta-args>              := <meta-arg> { ',' <meta-arg> }* [ ',' ]
<meta-arg>               := <fn-arg>
                          | ? { <toks> }*, except ',', unless handled by the pattern ?
```

A meta-invocation allows a meta-function to be called and if the resulting value is a code representation, parsed into the AST.

The meta invocation comes in 3 forms:
- a version using `{}`, which may be used anywhere code is allowed.
- 2 versions using `()` and `[]` respecively, these can only be used in locations where an expressions is allowed.

> _Implementation_: Since the compiler cannot determine the exact input of a meta-function during parsing, the parsing happens only later on in the process

## Meta variables [↵](#metaprogramming)

A meta variable is a special variant of a meta function which takes in either:
- no parameters
- a single fragment

This also counts when these are the only non-optional parameters, any optional parameters will get there default value in this case.

When a meta-variable has no parameters, it will just be replaced by the code generated by the metafunction.

When a meta-variable is followed by a fragment as its sole parameter, it will take the fragment immediatally after it, and consume it as an argument.
The variable and the subsequent fragment will then be replaced by the output of the meta-function.

> _Note_: For the purpose of parsing a meta-variable with a subsequent expression, it will take in the entire expression following it, i.e. it has the lowest precedence

> _Example_
> ```
> meta fn one() -> i32 { 1 }
> 
> #one;
> // equivalent to
> #one();
> 
> meta fn cat(expr: meta.Expression) {}
> 
> #cat 1 + 2;
> // equivalent to
> #cat(1 + 2);
> 
> meta fn with_opt(val: i32 = 2) {}
> 
> #with_opt;
> // equivalent to
> #with_opt();
> ```

## Meta attributes [↵](#metaprogramming)
```
<meta-attribute> := { <attribute> }* 'attr' [ '(' <meta-attr-role> [ ',' <meta-hygiene-info> ] ')' ] <ext-name> <meta-signature> [ '->' <type> ] <block>
<meta-attr-role> := 'full'
                  | 'peer'
                  | 'member'
                  | 'member_attr'
                  | 'accessor'
                  | 'derive' '(' <path> { ',' <ext-name> [ <meta-signature> ] } ')'
```

A meta-attribute is variant on a meta-functions which is applied to code as an [attribute].

Unlike meta-functions, a meta-attribute needs to follow a specific signature, as it is applied in a fixed set of locations.
The signature must always take in one of the following types as its first parameter, with the label being ignored:
- `meta.Ast`, the label will be ignored
- any fragment supporting attributes, this will limit the attribute to any fragment of that kind.
  If no meta-attribute with the same named exists that takes in a `meta.Ast`, multiple meta-attribute with the same name but different fragments types are allowed

This parameter will contain the fragment on which it is applied.

> _Example_
> ```
> attr fn foo(val: meta.fragment.Struct) {}
> 
> // duplicate name allowed, but only since it's a meta-attribute with a unique fragment type
> attr fn foo(val: meta.fragment.Union) {}
> 
> // this meta-attribute will result in only a single variant being allowed of its role
> // attr fn foo(val: meta.fragment.Ast) {}
> ```

### Attribute roles [↵](#meta-attributes-)

Since meta-attribute can only be applied as an attribute, they may use an identifier with an extended name.

In addition, each attribute can declare which role of attribute it is.
If no explicit role is provided, the attribute is not allowed to generate any resulting value.

> _Example_: basic attribute (no role)
> ```
> attr fn print_name(ast.Ast) { ... }
> 
> @attr
> struct Foo;
> ```
> assuming that the attribute prints the name during compilation, the code will be unchanged (with the exception of the attribute not being present anymore).
> It will however output the following in the compiler's output
> ```
> Struct name: Foo
> ```


The following attribute roles are available:
- `full`: creates a fragment which will replace the entirety of the fragment the attribute is applied to, meaning the original fragment is consumed by the attribute.
  Since a `full` attribute could cause for confusing results, it is possible to enable a compiler flag to output a warning on the expension of a `full` meta-attribute
  > _Example_
  > ```
  > attr(full) fn full(val: meta.Ast) -> meta.Ast { ${ struct Abc; } }
  > 
  > // replaces the entirety of `struct A;` with the result of the attribute
  > @full
  > struct A;
  > ```
  > the attribute will replace the entirety of the fragment it is applied to, resulting in:
  > ```
  > struct Abc;
  > ```

- `peer`: creates a fragment which will be located within the same scope as the fragment is it applied on
  > _Example_
  > ```
  > attr(peer) fn impl_outer(val: meta.Ast) -> meta.fragment.Implementation { 
  >     ty := get_item_type(val);
  >     #{
  >         impl $ty {
  >             fn peer_added_fn() { println("Function added by a peer attribute"); }
  >         }
  >     }
  > }
  > 
  > @impl_outer
  > struct Foo {}
  > ```
  > adds an implementation in the same scope as `Foo`, resulting in:
  > ```
  > struct Foo {}
  > 
  > impl Foo {
  >     fn peer_added_fn() { println("Function added by a peer attribute"); }
  > }
  > ```

- `attr`: creates one or more attributes which will be applied to the fragment the attribute is applied to
  > _Example_
  > ```
  > attr(attr) fn foo(val: meta.Ast) -> [2]meta.fragment.Attribute {
  >     [
  >         #{ @bar },
  >         #{ @baz },
  >     ]
  > }
  > 
  > @foo
  > struct A;
  > ```
  > resulting in
  > ```
  > @bar
  > @baz
  > struct A;
  > ```

- `member`: creates a fragment which will be located within the current fragment, and serve as a member of it.
  This requires the fragment it is applied on to support the fragment produces as a member.
  > _Example_
  > ```
  > attr(member) fn impl_inner(val: meta.Ast) -> meta.fragment.ImplementationField { 
  >     ty := get_item_type(val);
  >     #{
  >         impl {
  >             fn peer_added_fn() { println("Function added by a peer attribute"); }
  >         }
  >     }
  > }
  > 
  > @impl_inner
  > struct Foo {}
  > ```
  > adds an implemention inside of `Foo`, resulting in:
  > ```
  > struct Foo {
  >     impl {
  >         fn peer_added_fn() { println("Function added by a peer attribute"); }
  >     }
  > }
  > ```

- `member_attr`: adds attribute on any member of the current fragment.
  The meta-attribute decides which member the attribute will be applied to, by providing a [key-value pair] with the members it will be applied to.
  The meta-attribute must return a value of type `meta.MemberAttributes`.
  > _Example_
  > ```
  > attr(member_attr) fn add_attrib(val: meta.Ast) -> meta.MemberAttributes {
  >     meta.MemberAttributes([
  >         "bar" => #{ @foo_attr },
  >         "quux" => #{ @quux_attr }
  >     ])
  > }
  > 
  > @add_attrib
  > struct Foo {
  >     bar: i32,
  >     baz: i32,
  >     quux: i32,
  > }
  > ```
  > adds an attribute to a select set of members, resulting in:
  > ```
  > struct Foo {
  >     @bar_attr
  >     bar: i32,
  >     baz: i32,
  > 
  >     @quux_attr
  >     quux: i32,
  > }
  > ```

- `accessor`: adds an accessor to the property on which the accessor is applied. This attribute may only take in a `meta.Property` fragment.
  The attribute may also be provided on a field, which will convert the field to a [field property] with the given accessors.
  The attribute must return a value of one of the following types:
  - `meta.fragment.PropertyAccessor`
  - `[N]meta.fragment.PropertyAccessor`
  - `DynArr[meta.Fragment.PropertyAccessor]`
  > _Example_
  > ```
  > attr(accessor) fn add_set(val: meta.fragment.Property) -> meta.fragment.PropertyAccessor {
  >     #{
  >         set { self.val = $0 }
  >     }
  > }
  > 
  > struct Foo {
  >     val: i32,
  > 
  >     @add_set
  >     prop Value: i32 => self.val,
  > }
  > ```
  > , resulting in:
  > ```
  > struct Foo {
  >     val: i32,
  > 
  >     prop Value: i32 {
  >         get{ self.val },
  >         set{ self.val = $0 }
  >     }
  > }
  > ```

- `derive(Trait)`: implements the trait passed to the [`derive`] attribute on the type of the fragment it is applied to.
                   The name of that attribute cannot be applied directly, and must be done via the `derive` attribute.
                   This attribute may only be defined for traits that are defined in the current module.

  In addition, this trait may also define any helper traits which may be applied on any elements in the fragment it is applied to.
  These helper traits must also define the signature which they must adhere to.

  The attribute may only return a value of type `meta.fragment.ImplementationField`
  > _Example_
  > ```
  > attr(derive(Foo), foo_helper(count: i32)) fn foo_derive(val: meta.Ast) -> meta.fragment.Implementation {
  >     names_and_vals := get_members_with_helper();
  >     #{
  >         impl as Foo {
  >             fn foo() {
  >                 println("auto-generated derive");
  >                 ${
  >                     println("tagged_member: \{$names_and_vals.0} => \{$names_and_vals.1}")
  >                 }*
  >             }
  >         }
  >     }
  > }
  > 
  > trait Foo {
  >     fn foo();
  > }
  > 
  > @derive(Foo)
  > struct Bar {
  >     a: i32,
  > 
  >     @foo_helper(1)
  >     b: i32,
  > }
  > ```
  > resulting in:
  > ```
  > struct Bar;
  > 
  > impl Bar as Foo {
  >     fn foo() {
  >         println("auto-generated derive");
  >         pritnln("tagged_member: b => 1");
  >     }
  > }
  > ```

Unless specified otherwise, all attribute roles must return either a `meta.Ast` or fragment type, which is allowed in the same location as the original fragment.

In addition to attributes allowing a common name when taking in different fragments types, an attributes name may also be shared with attribute that have a different role.
However, both attributes must have compatible signatures.
When applying an the attribute, this attribute will apply both roles to the fragment it is attached to.

If the attributes have roles that need to be in distinct locations, only the roles that are applicable on that fragment will be applied.

An attribute with the `full` roles cannot be declared alongside of the other roles for any given fragment.

> _Example_
> ```
> attr(member_attr) fn foo(ast: meta.Ast) -> meta.fragment.Attribute { ... }
> 
> // This is allowed, as the attribute has a different role
> attr(peer) fn foo(ast: meta.Ast) -> meta.Ast { ... }
> 
> // This is also allowed, but will only be applied when the attribute is applied on a member.
> attr(member) fn foo(ast: meta.Ast)
> 
> // applied both the `member_attr` and `peer` attributes
> @foo
> struct Bar {
>     // applies the `member` attribute
>     @foo
>     a: i32
> }
> ```

Some roles have limitation to where they can be applied, these are the following:
- `member` and `member_attr`: may only be applied on items which support members
- `accessor`: may only be applied on fields or properties
- `derive(...)`: may only be applied to items/types which can implement a trait

> _Note to LSP developers_: It could be useful to provide the programmer a way of know if a mete-attribute is of kind `full` or `accessor` and let them know code might be changed.

### Attribute parameters [↵](#meta-attributes-)

Since attribute may take in a set of nested values, each meta-attribute may take in a set of additional parameters.

This can be done in 2 ways:
- via a second param with type `meta.AttrMetaArgs`
- as list of parameters, with the expected types.
  Unlike functions, while labelless parameter need to be in the same order as in the signature, any optional parameters may be provided in any order after the labelless parameters.d

> _Example_
> ```
> attr fn foo(ast: meta.Ast, args: meta.AttrMetaArgs) { ... }
> 
> // when taking in attribute meta arguments, any arguments may be provided.
> @foo(bar, baz(a, b=2))
> struct A;
> 
> 
> attr bar(ast: meta.Ast, _ a: i32, _ b: f32, d: bool, c: &str) { ... }
> 
> @bar(1, 1.0, c = "hello", d = true)
> ```

Nested values can be done by taking in parameters with a struct type, which has the [`@attr_param`] attribute.

> _Example_
> ```
> @attr_param
> struct Bar {
>     a: i32,
>     b: f32,
> }
> 
> 
> @attr_param
> struct Baz {
>     b: f32,
>     c: bool
> }
> 
> attr fn foo(ast: meta.Ast, bar: Bar, baz: Baz) {}
> 
> @foo(bar(a = 1, b = 2), baz(b = 3, c = true))
> struct Struct;
> ```

Name only arguments are also supported, this is done by passing a value with one of the following types:
- `()`: this requires the name to be present for the attribute to be applied
- `meta.NameOnlyAttrMeta`: this will indicate whether a name is present, but has no associated value. It has one of 2 possible states:
  - `.None` or `null`: the name has not been provided
  - `.Present`: the name has been provided
- `meta.OptAttrMeta[T]`: this indicates a an argument in 3 possible states:
  - `.None` or `null`, the value has not been provided
  - `.NameOnly`: only the name of the value has been provided
  - `.Some(_)`: A value of type `T` has also been provided

> _Example_
> ```
> attr fn foo(
>     ast: meta.Ast,
>     name_only: meta.NameOnlyAttrMeta,
>     other_name_only: meta.NameOnlyAttrMeta,
>     a: meta.OptAttrMeta[i32],
>     b: meta.OptAttrMeta[i32],
>     c: meta.OptAttrMeta[i32]
> ) {
>     assert(name_only == .Present);
>     assert(other_name_only == .None);
> 
>     assert(a == null);
>     assert(b == .NameOnly);
>     assert(c == .Some(2));
> }
> 
> @foo(name_only, b, c = 2)
> struct Struct;
> ```

## Meta alias [↵](#metaprogramming)
```
<meta-alias> := ( 'meta' | ( 'attr' [ '(' <meta-attr-role> ')' ] ) ) 'fn' <ext-name> <meta-signature> '=' ( <meta-invocation> | ( <meta-invocation-expr> ';' ) )
```

Meta-functions/aliases may be aliased by assigning instead of requiring it to have a return type and body, it instead gets assigned a meta-invocation.
The alias will take over the return type, the attribute role, and any hygiene info from the aliasee.

Aliases are not support for meta-methods.

## Meta hygiene [↵](#metaprogramming)

Meta-hygiene defines how a meta-function/attribute will interact with surrounding code.
A meta-function/attribute is defined as hygienic when the resulting fragment does not interact with any code that is declared outside of the fragment.

Therefore, it is only possible for a meta-function to be _fully hygienic_ when it does not take in, or output any fragments, but instead just plain values (i.e. it does not interact with any code external to the meta-invocation).

> _Example_
> ```
> // `foo` is fully hygienic, as it does not rely on any externally declared values, nor returns a fragment which may add variables or items into the external code.
> meta fn foo(val: i32) -> type { ... }
> ```

The majority of meta-functions/attributes will at best be only partially hygienic, and a worst be fully unhygienic.

The compiler can be provided with additional info about the hygiene of a meta-function/attribute in 2 ways:
- by explicitly providing the compiler with hygiene info from the macro, by providing the context by providing a filled in `meta.HygieneInfo` stucture
- by providing hygiene info in the declaration of the meta-function/attribute

The meta-function/attribute is said to be _unhygienic_ when no hygiene info is provided.

The compiler may inform the user about any possible shadowing which may happen as a result of bad meta-hygiene usign the `meta_shadow` lint.

To ensure any variables defined within the resulting fragments which may conflict or shadow any external variables of items, the context provides the `make_unique_name()` helper functions.
Which will produce a fully unique names for any variables within the expanded meta-function/attribute.
In addition, the `make_unique_name(name: "")` variant allows the name to include a more descriptive name to aid debugging.

### Hygiene info at the declaration site [↵](#meta-hygiene-)
```
<meta-hygiene-info>          := <meta-hygiene-info-elem> { ',' <meta-hygiene-info-elem> }* [ ',' ]
<meta-hygiene-info-elem>     := <meta-hygiene-names>
                              | <meta-hygiene-field-convert>
<meta-hygiene-names>         := 'named' '(' [ <fn-receiver> ] <ext-name> [ <fn-signature> ] { ',' [ <fn-receiver> ] <ext-name> [ <fn-signature> ] }* ')'
                              | 'overloaded'
                              | 'prefixed' '(' <ext-name> { ',' <ext-name> } ')'
                              | 'sufficed' '(' <ext-name> { ',' <ext-name> } ')'
                              | 'arbitrary'
<meta-hygiene-field-convert> := 'field_convert'
```

Hygiene info may be provided at the declaration site within either the `meta` or `attr` specifiers.

The following hygiene info can be added:
- `names`: provides a list of names that the attribute will produce when it is being applied, the attribute may only produce elements with the names declared, but is not required to produce all.
  This is provided for either a meta-function, or a meta-attribute with the following roles: `peer`, `member`, and `full`.
  The following values can be used:
  - `named(...)`: provides a list of known names of the elements added by the attribute
  - `overloaded`: an element will be added with the same name as the fragment it is applied to
  - `prefixed(...)`: provides a list of names which will be prefixed by the name of the fragment it is applied to
  - `suffixed(...)`: provides a list of names which will be suffixed by the name of the fragment it is applied to
  - `arbitrary`: elements will be added with names that are only known during the application of the attribute, this allows for elements to be generated without knowing their names ahead of time.
- `field_convert`: indicates the accessor attribute may convert a field into a [field property]. This is provided for a meta-attribute with the `accessor` role.

These roles are mainly meant to provide the compiler with info ahead of time, and to allow additional control when it comes to meta-hygiene.

## Evaluating meta-functions/attributes [↵](#metaprogramming)

### Evaluation order [↵](#evaluating-meta-functionsattributes-)

Meta-functions do not have a fixed order in which they are evaluated.
On the other hand, meta-attributes are generally applied in a top-down, outer to inner order to the fragment they are attached to.

> _Example_: Meta-attribute evaluation order
> ```
> @peer_attr // (1)
> @add_member_attrs // (2)
> struct Foo {
>     @member_attr // (3)
>     // @added_member_attr (4)
>     a: i32
> }
> ```

Any attribute generated by another attribute will be places at the end of the current applied attribute.

It is however possible to specify the order of execution using the [`@meta_order`] attribute.

> _Example_
> ```
> attr fn foo() { ... }
> attr fn bar() { ... }
> 
> @meta_order(before(foo), after(bar))
> attr fn baz() { ... }
> 
> @foo
> @bar
> @baz
> struct A;
> ```
> the evaluation order, as defined by `baz`, will become
> ```
> @baz
> @bar
> @foo
> struct A;
> ```

### Limitations [↵](#evaluating-meta-functionsattributes-)

Whenever a meta-function/attribute is evaluated, it is done within a sandbox, which provides the following limitations:
- access to the file system is restricted to the root folder of the project
- no access to the network
- no shared state between different invocations (with the exception of multi-stage meta-functions)

In addition, for any other system interactions, e.g. retrieving a time-stamp or RNG source, it is limited to the functionality provided by the context.



[attribute]:        ./attributes.md
[`derive`]:         ./attributes/derive.md
[expression]:       ./expressions.md
[block]:            ./expressions/block-expressions.md
[key-value pair]:   ./expressions/key-value-expressions.md
[identifier]:       ./identifiers-paths.md#identifiers-
[path]:             ./identifiers-paths.md#paths-
[implicit context]: ./implicit-context.md
[item]:             ./items.md
[method]:           ./items/functions.md#methods-
[field property]:   ./items/properties.md#field-property-
[lit]:              ./literals.md
[lexical token]:    ./lexical-structure.md
[pattern]:          ./patterns.md
[statement]:        ./statements.md
[type]:             ./type-system/types.md
[1-ary tuple]:      ./type-system/types/composite-types/tuple-types.md
[vis]:              ./visibility.md

[`#parse_pattern`]: ./metaprogramming/meta-utilities.md "Todo: fix up link"
[`#match_pattern`]: ./metaprogramming/meta-utilities.md "Todo: fix up link"
[`@attr_param`]:    #meta-attributes- "Todo: fix up link"
[`@meta_order`]:    #evaluation-order- "Todo: fix up link"
[`@meta_stages`]: #mutli-stage-method-functions- "Todo: Fix up link"