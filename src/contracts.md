# Contracts

Contracts are used to find certain conditions that code needs to adhere to, these are generally split up in 2 main types.

Constracts evaluation happens in the following order:
1. Check if the contract group is active, if not, stop.
2. Check if the contract group has a predicate, and if it evaluates to `false`, stop.
3. Check the condition inside of the contract, if it evaluates to `false`, stop.
4. Finally report the validation via the contract group.

> _Note_: The exact API of contract groups still needs to be determined

> _Todo_: Add support for invariant contract items within type to ensure certain relation, during for example, struct initialization

## Function contracts [↵](#contracts-)

```
<fn-contract>             := <pre-contract> | <post-contract> | <invar-contract>
<pre-contract>            := 'pre' [ <contract-group-and-pred> ] '(' <expr> ')'
<post-contract>           := 'post' [ <contract-group-and-pred> ] '(' [ <name> '=>' ]  <expr> ')'
<invar-contract>          := 'invar' [ <contract-group-and-pred> ] '(' <expr> ')'
<contract-group-and-pred> := '[' <expr> [ 'if' <expr> ] ']'
                           | '[' 'if' <expr> ']'
```

Function contracts are composed out of 3 different kinds:
- preconditions
- postconditions
- Invariant conditions

A preconditions is used to define what values may be passed into a function.
Preconditions are evaluated before the function body gets executed.
For example what range an integer value should be in.

A postconditions is used to to check if the resulting state at the end of the function.
Postconditions may access unnamed return values by prepending the condition with `name =>`.
Postconditions are evaluated at after the function body, but before the function returns.
For example, checking if an a value was set to a value in a given range.

Postconditions also allow use of the contract capture operator to capture a value at the start of a function to use in the contract.

An invariant conditions is used to check the invariance of certain conditions, meaning that they cannot change the result of this condition over the functions lifetime.
Invariant conditions are evaluated when pre- or postconditions are evaluated.

Contracts may make use of the [contract capture operator]

## Asserts [↵](#contracts-)

```
<assert> := [ 'const' ] 'assert' [ <contract-group-and-pred> ] '(' <expr> { ',' <expr> }* ')' ';'
```

An assert is a special condition which may be used at any moment in code to check if a value adheres to given conditions.
They can be evaluated both at runtime or compiletime.

## Contract groups [↵](#contracts-)

Contract groups are used to manage the evaluation of a contracts.
The allow entire contracts to be disable, under which conditions they need to be evaluated, and how they should report an error.

Contract groups can be specified between `[` and `]` in an assert.
If no contract group is specified, the default contract groups is used, which has the following state:
- Only active when assertions are enabled via the assert configuration option
- Has no predicate, i.e. will always be checked
- Panics on a contract violation

> _Note_: The exact API of contract groups still needs to be determined.
> It also still needs to be determined how to override the default contract group.

## Constact predicates

In addition to declaring a contract group, each individual contract and assert may also have a predicate that needs to result in a `true` value to be able to run.
These are compile time expressions.

The are located together with a contract group, but are set after an `if`.

## Testing

Contract groups are also used for testing and are hooked into by the testing framework.

> _Note_: The testing framework has not entirely been figured out yet



[contract capture operator]: ./operators/special-operators.md#contract-capture-operator-