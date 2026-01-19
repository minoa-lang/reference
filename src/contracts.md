# Contracts
```
<fn-contract>             := <pre-contract>
                           | <post-contract>
                           | <invar-contract>
<contract-body>           := '(' <expr> ')'
                           | <block>
<contract-group-and-pred> := '[' <contract-group> [ <contract-pred> ] ']'
                           | '[' <contract-pred> ']'
```

Contracts represent a collection of condition to which a piece of code needs to adhere.
Contracts can be split into a couple groups:
- pre & post contracts
- invariant contracts
- free contracts, also known as asserts

A contracts body may can have 2 forms:
- a expression within parentheses
- a block, which must return a boolean value.
  It may additionally [`yield`] additional boolean values prior to exiting the block, allowing for more complex contract checks.

Contracts are evaluated in the following order:
1. if contracts are disabled using a compile-time switch, nothing happens and no contract will have been inserted
2. check if the contract group is active, if it is not active, stop
3. check if the predicate returns a `true` value, otherwise stop
4. evaluate the body of the contract, if no `false` value is returned, stop
5. report any contract violation to the contract group

Each step before 5. provided the contract to be stopped, allowing code execution to happen.

Certain contracts may make use of the [contract capture operator] to capture the value of a given expression as if it were evaluated at the start of the function, specifically:
- postconditions
- invariant contracts

## Pre & post contracts [↵](#contracts)
```
<pre-contract>  := 'pre' [<contract-control>] <contract-body>
<post-contract> := 'post' [<contract-control>] [ <name> '=>' ] <post-body>

<post-body>     := '(' [ <name> '=>' ] <expr> ')'
                 | <block>
```
Pre and post contracts are also known as pre-conditions and post-conditions respectively.
These can only be applied to [functions].

A prejcondition is applied to any of the parameters to a function, allowing it to check for any condition on the incoming data.
Preconditions get evaluated prior to entering the function body.

A post-condition on the other hand are used to check the state of values being returned from the function, in addition to any parameters to which the function has mutable access, including a [method receiver].
Preconditons get evualuated after the body has returned, but prior exiting the function.

A post-condition may also capture the resulting value and bind it to a given name.
This can only be done when the post condition does not directly have a block as its body.

Postconditions may make use of the [contract capture operator].

> _Example_
> ```
> fn foo(input: i32) -> i32
>     pre(input < 10)
>     post(val => val > 10)
> {
>     ...
> }
> ```
 
## Invariant contracts [↵](#contracts)
```
<invar-contract>       := 'invar' [<contract-control>] <contract-body>
<assoc-invar-contract> := <invar-contract> ';'
```

Invariant contracts are used to check the invariancy of certain states within the code.
They can therefore be applied to:
- [functions]
- [types]

When applied to a function, the contract will check the condition when both pre- and post-conditions are evaluated, ensuring the function does not invalidate any of these conditions.

Otherwise, when applied to a type, it will ensure the conditions on any value of the type at the start and end of any functions.
However, the compiler will try to avoid checking these conditions if the operation on the type is guaranteed to:
- not modify the type, or
- have been checked by a function call which modifies the type.

In any function to which an invariant contract applies, either directly or one applied on a type, the invariant contract will be evaluated:
- after any pre-conditions, but prior to entering the function body
- ater the body has returned, but prior to any post-conditions

Invariant contracts may make use of the [contract capture operator].

> _Perf_: Applying an invariant condition to a type alias or a composite type may incur a noticable overhead when used.

> _Example_
> ```
> struct Foo {
>     a:       i32,
>     not_one: i32,
> 
>     invar (self.not_one != 1);
> }
> 
> fn bar(val: &mut i32)
>     invar(*val in 0..12)
> {
>     if val == 0 {
>         *val = 2;
>     }
> }
> ```

## Asserts [↵](#contracts)
```
<assert>      := [ 'const' ] 'assert' <assert-body>
<assert-body> := '(' <expr> ')' ';'
                 | <block>
<assert-item> := <assert> ';'
```

An assert is a free-standing contract, can check if a piece of code adheres to its implied condition, and has both an item and expression form.
It may be executed at compile time when:
- the assert is located where only an item is allowed
- the assert is prefixed with a `const`

> _Example_
> ```
> fn foo() {
>     val := get_val();
> 
>     assert(val == 1);
> }
> 
> struct Foo(i32);
> 
> assert(size_of(Foo) == size_of(i32));
> ```

## Groups [↵](#contracts)
```
<contract-group> := <name>
```

Contract groups manage the evaluation of contracts in the following ways:
- defines how to respond in case an assert fails
- allows disabling/enabling of all contracts using the group

For a contract group to be used, it must first be registered to the application using [`#register_assert_group`], and must implement the [`ContractGroup`] trait.

If no contract group is specified, the default group is used, which responds in the following way:
- only active when assertions are enabled via command line argument, or when in a debug mode
- panics on a contract violation

The default group can be overriden by assigning a value to [`#set_def_assert_group`].

> _Note_: Contract groups are only available in location that have access to the [implicit context].
>         If the implicit context is used, the default group will be used, as this is handled differently compared to other user-defined contract groups

> _Example_
> ```
> struct CustomContractGroup {
>     impl as ContractGroup {
>         // Todo: Add implemenation here after trait is finalized
>         ...
>     }
> }
> 
> #register_contract_group(CustomContractGroup as custom);
> 
> fn foo() {
>     // Uses the `custom` contract group defined above
>     assert[custom](1 + 1 == 2);
> 
>     // uses a custom contract group
>     assert(2 + 2 == 4);
> }
> ```

## Predicates [↵](#contracts)
```
<contract-pred>           := 'if' <expr>
```

A contract may be provided with a predicate which determines whether or not the contract should be evaluated.
This is an expression resulting in a boolean value, where `true` indicates the contract will be run.

The expression must be a compile-time expression.

> _Example_
> ```
> // Only run this assert if the `target_os` is windows
> assert[if #target_os == .windows](1 + 1 == 2);
> ```



[implicit context]:          ./implicit-context.md
[functions]:                 ./items/functions.md
[method receiver]:           ./items/functions.md#methods-
[contract capture operator]: ./operators/special-operators.md#contract-capture-
[types]:                     ./type-system/types.md

[`#register_assert_group`]:  #groups- "Todo: link to docs"
[`#set_def_assert_group`]:   #groups- "Todo: link to docs"
[`ContractGroup`]:           #groups- "Todo: link to docs"