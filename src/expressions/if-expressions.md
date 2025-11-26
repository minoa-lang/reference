# If expressions
```
<if-expr>     := <label> <if-sub-expr>
               | <res-if-expr>
<if-sub-expr> := [ 'const' ] 'if' <condition> <block> [ 'else' ( <if-sub-expr> | <block> ) ]
```

An `if` expression is a conditional branch in program control.

An if expression will evaluates its subsequent block when the condition evaluates to a `true` value, and will otherwise skip this block.
If an optional `else`  is supplied, it will executed its following `if` or block expression.

By default, all `if` expressions evaluated to a `()` value.
Branches may return a value, then cause a value of its corresponding type to be returned.
When this is done, all branches must return a value of the same type, meaning that the first branch will define the type of the `if` expression.

> _Example_
> ```
> if a == b {
>     foo();
> } else if a < b {
>     bar();
> } else {
>     baz();
> }
> ```

An `if` expression may also be explicitly marked as `const`, meaning that the condition will be evaluated at compile-time.

> _Implementation note_: `const` effectivly removes the condition check, meaning the compiler is free to remove the non executed branch from the compiled code.
>                         When a non`const` `if`-statement has a condition which can be resolved at compile time, this compliation behavior may also be applied to it.

> _Example_
> ```
> const if CONST0 == CONST1 {
>     foo();
> } else {
>     bar();
> }
> ```
> In the case where the condition resolves to true, this would be equivalent to
> ```
> // blocks are kept
> {
>     foo();
> }
> ```
> 
> Similarly, when a regular `if` expression has a compile-time known value, it may also be compiled into the above code.
> ```
> if CONST0 == CONST1 {
>     foo();
> } else {
>     bar();
> }
> ```


## Branch conditions [↵](#if-expressions)
```
<condition>      := <condition-elem> { ('&&' | '||') <condition> }
                  | '('<condition-elem> { ('&&' | '||') <condition> } ')'
<condition-elem> := <expr>
                  | <let-binding>
                  | '(' <condition> ')'
```
> _Note_: Any branch condition in the grammar above may be contained within any number of parenthesized expressions

A branch condition is 'chain' or expressions and let-bindings that results in a boolean value.

Within a branch condition, the `&&` and `||` operator have a special meaning, as they can propagate certain behavior to either the parts of the condition following each sub-expression, and the subsequent block.

For any `let` binding or optional shorthand within the condition, each operator handles it as follows:
- `&&`: propagates any new bindings created to all the following elements within the condition
- `||`: propagates any common bindings between both sides of the operators to the outer conditional element/`if` expression

Any bindings which make it to the top level of the condition may be used in the subsequent block, but not in the `else` branch.

### `let`-bindings [↵](#branch-conditions-)
```
<let-bindings> := 'let' <pattern-no-top-alt> '=' ? <scrutinee> excluding lazy boolean operator expressions ?
```

Within a condition, a `let` binding allows for the matching of a value to a given pattern.
This will also propagate the bindings to the matched values to any subsequent condition element, based on the combining operators.

A `let` binding evaluates to `true` whenever the scrutinee matches the provided pattern.

Similarly to the pattern within a [`match`] expression, an alternative pattern (patterns separated with a `|`) may appear within the pattern.
It also follows the same semantics as defined by the [`match`] expression.

> _Example_
> ```
> enum Foo {
>     A,
>     B(i32),
> }
> 
> foo := Foo.B(4);
> 
> if let Foo.B(x) = foo {
>     println("\{x}");
> }
> ```

### Optional shorthand [↵](#branch-conditions-)
```
<opt-condition> := [ <name> '=' ] <expr> '?'
```

Within a condition, any use the of the `?` operator is a shorthand for a `let` binding which will check the existence of a value.
This results in the value being implicitly upgraded into its pattern matched equivalent.

Additionally, it is possible to explicitly pass a name to bind the resulting value to.
If not passed, only named values will be bound.

By default, this is only supported for values of an [optional] or [result type].
Additional types may provide this functionality by adding the [`@bind_shorthand` attribute] to the type it will apply to.

> _Todo_: `@bind_shorthand` is passed a pattern which it will be matched to, with a single binding, e.g. for `@bind_shorthand(.Some(val))`, where `val` is replaced by the compiler

> _Example_
> ```
> foo: ?i32 = 4;
> 
> if foo? {
> 
> }
> // is equivalent to
> if let .Some(foo) = foo {
> 
> }
> 
> if val = foo? {
> 
> }
> // is equivalent to
> if let .Some(val) == foo {
> 
> }
> ```


## Result if expressions [↵](#branch-conditions-)
```
<ref-if-expr> := `if` <res-if-cond> <block> 'else' '(' <name> ')' <block>
<res-if-cond> := <let-binding>
               | [ <name> '=' ] <expr> '?'
```

Another variant of an `if` expression is the so-called _result `if`_, this allows for a value to be either map to the main or else branch of the `if` expression.
This can be done in 2 ways:
- if a `let`-binding is provided and the pattern is refutable, the main branch will be entered when the pattern matches.
  Otherwise the full unmatched value will be passed to the else branch.
- if a optional shorthand is used, it allows for a value with 2 possible variants to be mapped to the branches.
  The main branch will be executed if the shorthand results in a match, otherwise it will be passed to the `else` branch.

  This can be compared the optional shorthand specified above, but will also bind the 'error' value to the `else` branch.

  Additionally, it is possible to explicitly pass a name to bind the resulting value of a `true` branch to.
  If not passed, only named values will be bound.
  
  By default, this is only support for values of a [result type].
  Additional types may provide this functionality by adding the [`@bind_shorthand` attribute] to the type it will apply to, requiring the optional second argument to be passed.

  > _Todo_: `@bind_shorthand` is passed 2 patterns, which it will be matched to, with each having a single binding, e.g. for `@bind_shorthand(.Ok(val), .Err(val))`, where `val` is replace by the compiler

The `else` branch must always define a name to bind a value to.

> _Example_
> 
> The `let`-binding version
> ```
> enum Foo { A(i32), b(i32), C(i32) }
> 
> fn get_foo() -> Foo { .A(1) }
> 
> foo := Foo.A(1);
> 
> if let .A(val) = get_foo() {
> 
> } else (foo) {
>     // `foo` can be used, even if in a regular `if` expression, it would otherwise have been discarded
>     // `foo` has the value returned by `get_foo()`, not a pattern matched one
> }
> ```
> 
> The shorthand version
> ```
> foo: i32!bool = true; // Result[i32, bool]
> 
> if foo? {
>     // valid case code
> } else (err_code) {
>     // error case code
> }
> ```
> This can be thought of as being functionally equivalent to
> ```
> match foo {
>     .Ok(foo) => {
>         // valid case code
>     },
>     .Err(err_code) => {
>         // error case code
>     }
> }
> ```



[`match`]:                         ./match-expressions.md
[optional]:                        ../type-system/types/abstract-types/optional-types.md
[result type]:                     ../type-system/types/abstract-types/result-types.md

[`@bind_shorthand` attribute]:     #optional-shorthand- "Todo: attribute does not exist yet"