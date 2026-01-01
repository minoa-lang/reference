# Operators
```
<operator> := ? <punct>, except disallowed sequences ?
```

An operator is a sequence of non-alphanumeric characters (with certain exceptions), which represents an operation on 1 or 2 attached expressions.
Operators are split into 4 kinds:
- prefix unary: acts on the expression coming after it
- postfix unary: acts on the expression coming before it
- infix: acts on the expressions on both sides
- assignment: modifies the expression on the left-hand side using the operator on the right-hand side. The left hand operand needs to be a [place expression].

Similarly to names, an operator will always exists out of the longest possible sequence of consecutive characters, unless explicitly split up.
This is done because operator may be defined by the user, and this might cause ambigous syntax, if multiple operators appear directly next to eachother.

## Whitespace sensitivity [↵](#operators)

Unlike most syntax, operators are impacted by the whitespace surrounding them, in cases where the operator kind can not be exclusively determined.

In these cases, the following rules determine how the operator is determined:
- if non-operator expressions appear on both sides of an operator, it will be interpreted as an infix operator
- otherwise, if 2 or more operator expressions are located next to each other, where the 2 operators are next to each other, the following applies
  - if the operator is only has whitespace on one side, and the other is an expression, it will be interpreted as a unary expression.
    If 2 operator expressions are next to each other, and both adhere to this rule, the operators will be ambiguous, an will result in an error.
  - otherwise, it will be interpreted as an infix operator

> _Example_
> 
> The following uses of `+` will be interpreted as an infix operator
> ```
> a+b;
> a + b;
> a^ + b;
> a + &b;
> ```
> 
> The following uses of `+` will be interpreted as a unary operator
> ```
> a - +b;
> a+ - b;
> ```
> 
> And the following will be ambiguous:
> ```
> a- +b;
> ```

## Operator sets [↵](#operators)
```
<op-set>      := { <attribute> }* [ <vis> ] 'op' 'trait' <name> [ <op-rhs-spec> ] [ '|' <name> ] [ <where-clause> ] '{' <op-members> '}'
               | { <attribute> }* [ <vis> ] 'op' 'trait' <name> [ <op-rhs-spec> ] ':' <path> { '&' <path> }* [ <where-clause> ] '{' <op-members> '}'
<op-rhs-spec> := '[' [ '=' ] <type> ']'
               | '[' '_' ']'
<op-members>  := <op-member> { ',' <op-member> } [ ',' ]
<op-member>   := <op-decl> | <op-contract>
```

An operator set is a collection of individual operators and contracts which those operators must follow.

Operators set also create an interface which is associated with the operator set, which is used to implement individual operators.

Each operator set and their members share the same visibility.

Operator sets can be defined in 2 ways:
- as a standalone operator set.
  This set may define the precedence the infix operators have.
- as an extended operator set, which requires other operators sets to be supported.

Operator precedences are only relevant for `infix` operators, as all other operator kinds have a hardcoded precedence as defined by the [expressions precedences].

Whenever an operator set contains an infix or assignment operator, the entire operator set will include an implicit generic parameter named `Rhs`, which defaults to `Self`.
This second parameter may additionally either be:
- explicitly defined, as `<type>`, or
- given another default value, as `= <type>`, or
- have the default value of `Self` removed, as `_`

If the generic parameter is not explicitly defined, the operator set may not contain any prefix or postfix operators.

> _Note_: All operators, with the exception of [special operators] are defined in this manner

### Operator declarations [↵](#operator-sets-)
```
<op-decl>     := [ <op-mods> ] <op-kind> 'op'<operator> ':' <name>  [ '->' <type> ] [ = <expr> ] ';'
               | [ <op-mods> ] <op-kind> 'op' <operator> ':' <name> ':=' <expr> ';'
               | [ <op-mods> ] 'assign' 'op' <operator> ':' <name> [ = <expr> ] ';'
<unary-kind>  := 'prefix'
               | 'postfix'
               | 'infix'
```

An operator declaration declares the actual operator, along side with some additional info:
- the modifiers for the operator
- the kind of operator
- an optional return type
- an optional default implementation

The operator must be defined as the 4 different kinds:
- `prefix`
- `postfix`
- `infix`
- `assign`

This is then followed by the operator's syntax when used in an expression, alongside a the name of the function implementing the operator.

All operators, except for assignment operators, may have their return type explicitly defined.
If this is not done, this will be given the common `Output` type, provided as a type alias, by the operator set.

And finally, the operator may be given a default implementation.
This default implementation is limited to, similarly to a trait implementation, only use the functionality defined by its bounds.

> _Example_
> ```
> op trait AddAssign : Add {
>     // fine, as `Add` is a bound and provides the `+` operator
>     assign op += : add_assign = { $0 = $0 + $1 };
> }
> 
> op trait AddAssign2 {
>     // error: cannot use the operator `+`
>     assign op += : add_assign = { $0 = $0 + $1 };
> }
> ```

It is also possible to provide a direct implementation for an operator, in case this may not be overriden by an implementation.

> _Example_
> ```
> op trait BothSidesUnary {
>     prefix op ^ : pre_unary;
> 
>     // the postfix `^` op will now always be defined in terms of the prefix op, regardless of implentation
>     postfix op ^ : post_unary := ^$0;
> }
> ```

### Type aliases [↵](#operator-sets-)
```
<op-type-alias> := <trait-alias>
```

Since different operators might require slightly different return values, which are not variations of the default `Output` alias, it is possible to define additional type aliases that can be returned.

These type aliases must be referenced by at least 1 operator's return type.

If an explicit `Output` type alias is defined, this will take the place of the otherwise auto-generated variant of it.
This allows the output type to be provided with additional bounds, and/or given a default value.

By default, if there is any infix or assign operator that does not have a default value, and no `Output` alias is defined, an `Output` type alias with only a `Sized` bound is defined.

### Operator contracts [↵](#operator-sets-)
```
<op-contract> := 'invar' <expr> ';'
               | 'invar' <block-expr>
```

An operator set may also provide contracts to which the operators defined by the set must adhere.
Each contract must refer to at least 1 operator defined within the contract, where it operates on at least 1 provided value.

Values can be provided by using [implicit closure parameters].
When using infix or assignment operators, the contract will automatically infer whether a parameter should have the left-hand or right-hand type based on the location of the parameter within the contract's expression.

The contracts must be valid for any possible value passed as one of the implicit parameters.

When an operator set extends another operator set, additional contracts may be defined for those operators.

_Example_
```
op trait Compare {
    infix op == : eq -> bool;

    // fine, `==` is defined within the contract
    // the middle `==` is not concidered, as it does not directly operator on a `$n` value, but rather on 2 boolean values (in this case).
    invar ($0 == $1) == ($1 == $0);

    // error: no operator defined in `Compare` is referenced
    invar $0 != $1;
}

op trait ExtCompare : Compare {
    // also valid, as it used an operator defined in `Compare`
    invar ($0 == $1) == ($1 == $0);
}
```

## Operator kinds [↵](#operators)

Each operator kind, which defines how the operator can be used.

### `prefix` [↵](#operator-kinds-)

A prefix operator is applied in front of the operand it applies to.

As defined by the [expressions precedences], a prefix expression will be applied after any postfix operator.

### `postfix` [↵](#operator-kinds-)

A postfix operator is applied at the back of the operand it applies to.

As defined by the [expressions precedences], a postfix expression will be applied before any prefix operator.

### `infix` [↵](#operator-kinds-)

An infix operator is applied to the operand on either side of it.

Infix operators need to have their precedences defined explicitly, or will otherwise the infix operator will have its own precedence, which is unrelated to any other precedence.
Wether the operator will first evaluate the left- or right-hand operand is also specified by its precedence.

### `assign` [↵](#operator-kinds-)

An assign operator is a variant on an infix operator.

Instead of taking in 2 values, doing an operation on them, and returning the result, an assign expression directly modifies the left-hand operand.
In the expression, the left-hand operand is known as the _assignee_ and the right-hand operand as the _assigned value_, i.e.
```
<assignee> = <assigned value>
```

The assigned value will always be evaluated before the assignee.

Although the assignee is passed into operator's implmentation as a mutable reference, under the hood, this is handed by having the value be [borrowed uniquely].
Meaning that any use of it in the assigned value, may occur until the first assignment within the operator's implementation.
This is specifically meant to support a `lazy assign` operator.

> _Note_: While there are no limitations to the syntax of this kind of operator, it is recommended to end an assignment operator with a `=`, so make them easier to distinguishe

## Operator modifiers [↵](#operators)
```
<op-mods> := 'lazy'
           | [ 'lazy' ] 'chain'
           | [ 'lazy' ] 'consume'
           | `by_ref`
```

Operators may be provided with certain modifiers which affect how a will utilize its operands.

### `lazy` [↵](#operator-modifiers-)

The `lazy` modifiers specifies that the second parameter will be taken in as a [lazy function parameter].
This allows the operator to skip evaluating the second operand, when the first operand would indicate that it is not needed.

_Example_
```
op trait Elvis {
    lazy op ?: : elvis = {
        if is_valid(a) {
            a
        } else {
            b
        }
    }
}

// `complex_calculation` will only be evaluated if `a` indicates it should be evaluated
c := a ?: complex_calculation();
```

This modifier is only allowed to be used by `infix` and `assign` operators.
This modifier is incompatible with the `by_ref` modifier.

### `chain` [↵](#operator-modifiers-)

The `chain` modifier indicates that the left-hand side operand will be moved into the right-hand operand.

The examples for each variant will assume the following operator:
```
op trait Chain {
    chain op |> : chain;
}
```

A chain operator works differently depending on what expression the right-hand operand is, specifically if the type adheres to `Rhs is Fn(T0, ..., Tn)`, with `N = n + 1` operands, or any other call trait.
This will be handled as one of the following:
- [call expression], it depends on what the expression is that is being called:
  - if the name and signature matches a method that can be called on the left-hand operand, it will use that.
    Member calls are only supported when the function interface only has 1 operand.

    To handle multiple operands, use an explicit call on `$0` and manually ensure `$1..$n` are passed to the method.
    
    To avoid a method being looked up and instead call a function, the function needs to be explicitly [curried].

    > _Example_
    > ```
    > struct Foo {
    >     fn(&self) bar() {} // (1)
    >     fn(&self) bar(_ val: i32) {} // (2)
    > }
    > 
    > fn bar(_ val: Foo) {} // (3)
    > 
    > // value has method (1) called on it
    > Foo{} |> bar;
    > Foo{} |> bar();
    > 
    > // value has method (2) called on it
    > Foo{} |> bar(1);
    > 
    > // (3) is requires the function to be explicitly curried
    > Foo{} |> bar(_);
    > ```

  - function: this will pass the left-hand operand as the first argument to the function, this requires:
    - the function has at least `N` parameters
    - the first `N` parameters are labelless

    In additon, if the function has more than `N` parameters, the call is expected to leave the first `N` parameters out, or mark them as `_`.
    This will automatically [curry] the function to one which takes in only the first argument.

    > _Example_
    > ```
    > fn foo(_ val: i32) {} // (1)
    > fn foo2(_ val: i32, _ other: i32) {} // (2)
    > 
    > // value is passed to (1)
    > 1 |> foo;
    > 1 |> foo(_);
    > 
    > // value is passed to (2)
    > 1 |> foo(2);
    > 1 |> foo(_, 2);
    > ```
- [closure expression]: The closure must take exactly `N` parameter, either explicitly defined, or using `$0..$n`
  > _Example_
  > ```
  > 1 |> fn{ (val: i32) => val + 1 };
  > 
  > 1 |> fn{ $0 + 1 };
  > ```
- an expression containing an [implicit closure parameter] within it, will convert the expression to a closure
    > _Example_
    > ```
    > 1 |> $0 + 1;
    > ```

If none of the above special cases 

This modifier is only allowed to be used by `infix` operators.
This modifier is incompatible with the `consume` and `by_ref` modifiers.

### `consume` [↵](#operator-modifiers-)

The `consume` modifers indicates tha the right-hand operand will be moved into the left-hand operand.
In additon, the right hand operand may consist out of a [comma expression].
This comma expression will continue, until the next occurance of the operator that is not nested in a lower level.

To support this, the right-hand parameter may be declared as any of the following:
- as a [parameter pack]
- as a [tuple type], including [named tuples].
- as a [slice type]

When a tuple type is provided, if the tuple contains with an optional type, these fields do not need to be provided when using the operator.
Any values that are not provided, will then be passed a value of `null`.

> _Note_: if you want to provide the ability to pass optional values in any order, this should be done with a structure with setting that is passed

> _Example_
> ```
> op trait Consume {
>     consume infix op <| : consume;
> }
> 
> foo <| 1;
> foo <| 2, 3, 4;
> foo <| 10, option0: 42, option1: 1337;
> foo <| 12, .{ any: 1, order: 2, options: 4 };
> ```

This modifier is supported for all operators, except `postfix`.
This modifier is incompatible with the `chain` and `by_ref` modifiers.

### `by_ref` [↵](#operator-modifiers-)

The `by_ref` modifier indicates that any value must always be used as a reference, and may never be moved or copied into the operator.
This also means that the operator will implicitly take a reference of each operand.

This modifier is supported for all operators.
This modifier is incompatible with other modifiers.

## Implementing operators [↵](#operators)

When implementing an operator, each has an expected signature, defined by both its kind and modifiers.
Below `Rhs` represent the type of the right-hand side, and `Output` will be any type alias that is defined within the operator.

op kind   | modifier  | signature
----------|-----------|-------------------------------------
`prefix`  | n/a       | `fn(self) name() -> Output`
`prefix`  | `consume` | `fn(self) name() -> Self`
`postfix` | n/a       | `fn(self) name() -> Output`
`infix`   | n/a       | `fn(self) name(rhs: Rhs) -> Output`
`infix`   | `consume` | `fn(self) name(rhs: Rhs) -> Self`
`assign`  | n/a       | `fn(&mut self) name(rhs: Rhs)`

## Disallowed operator sequences [↵](#operators)

While most punctionation sequences are allowed, there are some restriction for punctuation which is not allowed.
The following sequences are not allowed:
- `.`
- `,`
- `:`
- `:=`
- `=`
- `=>`
- `->`
- `?.`
- `\`
- `\.`
- `$` (with the exception of the `core`'s [contract capture operator])

Additionally, any user-defined operator sets must not collide with any operators defined within the `core` library.

## Operator scoping and use [↵](#operators)
```
<op-use>  := 'op' 'use' <use-root> [ <op-path> ] ';'
<op-path> := '.' '{' <operator> { ',' <operator> } '}'
```

Operator have different scoping rules to other symbols, as they are split up in 2 types of symbols:
- the operators self
- the operator traits

The actual operators are not relative to any module or item, but are only relative to the [main module].
Meaning that all operator sets are located in a single namespace.

The operator traits on the other hand, as scoped like any other symbol.

This also requires operator sets to be imported using a special variant of the [use item].
This variant may either import all operators from a given library, or specify individual operators to import.
This variant in only allowed to be located within the [main module], and may not be located in any other item.

This is done to ensure the use of a given operator stays consistent across an entire library.

The `core` operators are imported by default via the `core`'s prelude.


[operators section]:           #operators
[expressions precedences]:     ./expressions.md#expression-precedence--operator-evaluation-
[place expression]:            ./expressions.md#place-expressions-
[call expression]:             ./expressions/call-expressions.md
[closure expression]:          ./expressions/closure-expressions.md
[implicit closure parameter]:  ./expressions/closure-expressions.md#implicit-parameters-
[implicit closure parameters]: ./expressions/closure-expressions.md#implicit-parameters-
[comma expression]:            ./expressions/comma-expressions.md
[parameter pack]:              ./generics.md#parameter-packs-
[lazy function parameter]:     ./items/functions.md#parameter-specifiers-
[curried]:                     ./items/functions.md#partial-application-currying-
[curry]:                       ./items/functions.md#partial-application-currying-
[use item]:                    ./items/use.md
[special operators]:           ./operators/special-operators.md
[contract capture operator]:   ./operators/special-operators.md#contract-capture-
[main module]:                 ./package-structure.md#main-module-
[tuple type]:                  ./type-system/types/composite-types/tuple-types.md
[named tuples]:                ./type-system/types/composite-types/tuple-types.md#named-tuples-
[borrowed uniquely]:           ./type-system/types/function-like-types/closure-types.md#unique-immutable-borrows-in-captures-
[slice type]:                  ./type-system/types/sequence-types/slice-types.md