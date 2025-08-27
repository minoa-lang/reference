# Operators

An operator is a set of non-alphanumeric symbols (with some exceptions) that can represent an operation to 1 or 2 expressions.
They are generally split into 3 categories:
- prefix unary operators, these come before a sub-expression
- postfix unary operators, these come after a sub-expression
- infix binary operators, these come betweeen 2 sub-expressions

Operators must be separated by non-operator symbols, otherwise they will be interpolated as 1 single operator, meaning that multiple prefix expression, operators must be separated by parentheses.
In the future, an additional way of separating these might be decided.

Operators, unlike other syntax, is dependent on whitespace, as it is used to consistently differentiate between prefix, infix, or postfix.
These rules are: 
- unary operator need to be directly attached to the value they operator on, i.e. no whitespace may be between them
- whitespace around binary operators need to be consistent, there is either no whitespace between it and the values, or there is whitespace on either end.

> _Todo_: Specify which operators are include in `minoa:core.minimal`

## Operator items [↵](#operators)

```
<op-set>      := <base-op-set> | <ext-op-set>
<base-op-set> := { <attribute> }* 'op' 'trait' <name> [ '|' <name> ] '{' <op-elems> '}'
<ext-op-set>  := { <attribute> }* 'op' 'trait' <name> ':' <simple-path> { '&' <simple-path> }* '{' <op-elems> '}'

<op-elems>    := <op-elem> { ',' <op-elem> } [',']
<op-elem>     := <op-decl> | <op-contract>
<op-decl>     := [ <op-mod> ] <op-kind> 'op' <operator> ':' <name> [ '=' <expr> ]
<op-mod>      := 'lazy' [ 'chain' | 'consume' ]
               | 'chain'
               | 'consume'
<op-kind>     := 'prefix' | 'postfix' | 'infix' | 'assign'

<op-contract> := 'invar' <block-expr>
```

Operator sets are used to declare new operators and their related properties.
The majority of core operators are also implemented using this system.

Operator sets also create the associated trait and underlying methods to use these within code.
All operator sets and associated operators are public symbols.

An operator set is allowed to define one of the following:
- A precedence wich will be used by all infix operators within the set, or
- Other operators set that this set extends, and therefore requires to have implemented to implement the 'derived' operator set.

Each operator within the set must have the following properties:
- An operator kind
- The operator's punctuation in code
- The name of the corresponding method (needs to be unique for each operator)

And optionally contains:
- return type
- default implementation

Operators are defined within this set, starting with the kind of operator, these are:
- Prefix operators that apply to the expression on the right of them
- Postfix operators that apply to the expression on the left of them
- Infix operators that apply to the expressions on either side of the operator
- Assign operators that modify the expression on left of them using the expression on the right

Precedence is only applied on the infix operators. Prefix, postfix, and assign expressions have hardcoded precedences, and can therefore not be defined explicitly.

If no return type is provided, the operator will return the operator trait's associate `Output` type alias. Assign operator cannot have a return type.

When any infix operator is defined, but no precendence of set to extend is provided, the expression containing the infix operator and its operands will be required to be surrounded by parentheses, if these are other operator expressions.

> _Note_: While extending another set allows new operators to be added, any operator contained within the set being extended cannot be overriden

Operator sets can also define a set of invariant contracts, to which all contained operators must adhere to, for example: commutativity.

TODO: disallow identical looking sets of characters

### Operator modifiers [↵](#operator-items-)

Each operator may have some additional modifiers applied on them to define how they will handle their operands

#### `lazy` [↵](#operator-modifiers-)

This indicates that that the right-hand operand will be passed to the operator as a [lazily parameter].

This is only allowed on infix and assign operators.

#### `chain` [↵](#operator-modifiers-)

A chaining expression indicates that the left-hand operand will be passed on to callee.

How the chaining is handled, depends on the right-hand operand, which is one of the following:
- function: The left-hand operand will be passed as the first argument of the callee, this requires that:
  - the function has at least 1 argument
  - its first argument is label-less
  - only has the arguments after the first argument passed
- method: The left-hand operand will used as its receiver
- closure: the closure has exactly 1 argument, either explicitly defined, or it requires for `$0` to be used within the closure
- autoclosure: the closure is created if the implicit `$0` variable is used
  
Otherwise this operator is called just like any other infix operator.

This is only allowed on infix operators.

#### `consume` [↵](#operator-modifiers-)

This indicates that the operator will receiver a right-hand operand, consisting of a list of comma-separated expression.
These values will be consumed until an expression on its right side is encountered with a lower precedence.

The values may be provided either as:
- a [parameter pack]
- a tuple, if not all elements are provided, but the elements they represent are optional, these values will default to `.None`
- an iterable type that can generate a number of values

In addition, a `consume` operator may also be directly followed by a loop expression that return a value at the end of each iteration, or using a `break` expression.
In this case, the loop will automatically be wrapped using an iterable closure.

While a comma normally has the lowest precedence, for consuming operators, this is not taken into account when selecting a cut-off point.

Unlike non-`consume` operators, this returns the left-hand size

This is not allowed for postfix operators

#### `by_ref` [↵](#1operator-modifiers-)

This indicates that the operator will always take a reference to both values, as it does not need them to be moved into the operator.

An example of this are the comparison operators.

### Implementing operators on types [↵](#operator-items-)

Implementing operators on a type is done by implementing it's trait, like usual.

It the operator includes an infix or assign type, they both take the same `Rhs` argument within the trait, so must be specified for anything other than the type being implemented.

In case a return type is not defined on at least 1 operator (except for assign operators), the Output type for the operator needs to be defined as `type Output = ...;`

Below is a table of operators and their respective method signature

 Op Type | modifier  | Signature
---------|-----------|---------------------------------------------
prefix   | n/a       | `fn name(self) -> Self::Output`
prefix   | `consume` | `fn name(self) -> Rhs`
postfix  | n/a       | `fn name(self) -> Self::Output`
infix    | n/a       | `fn name(self, other: Rhs) -> Self::Output`
infix    | `chain`   | `fn name(self, other: Rhs) -> Rhs`
infix    | `consume` | `fn name(self, other: Rhs) -> Self`
assign   | n/a       | `fn name(&mut self, other: Rhs)`

### Allowed characters. [↵](#141-operator-items-)

This defines a list of characters that are allowed to be used in user defined operators.

The characters generally include mathematical operators, miscellaneous characters, dingbats unicode blocks, ect.

More inf
```
<custom-operator>   := <operator-head> { <operator-continue> }*
                     | '.' { <operator-continue> | '.' }*
<operator-head>     := '!'
                     | '%'
                     | '&'
                     | '*'
                     | '+'
                     | '-'
                     | '/'
                     | '<'
                     | '='
                     | '>'
                     | '?'
                     | '|'
                     | '~'
                     | ? U+00A1 - U+00A7 ?
                     | ? U+00A9 ?
                     | ? U+00AB ?
                     | ? U+00AC ?
                     | ? U+00AE ?
                     | ? U+00B0 ?
                     | ? U+00B1 ?
                     | ? U+00B6 ?
                     | ? U+00BB ?
                     | ? U+00BF ?
                     | ? U+00D7 ?
                     | ? U+00F7 ?
                     | ? U+2016 ?
                     | ? U+2017 ?
                     | ? U+2020 - U+2027 ?
                     | ? U+2030 - U+203E ?
                     | ? U+2041 - U+2053 ?
                     | ? U+2055 - U+205E ?
                     | ? U+2190 - U+23FF ?
                     | ? U+2500 - U+2775 ?
                     | ? U+2794 - U+2Bff ?
                     | ? U+2E00 - U+2E7F ?
                     | ? U+3001 - U+3003 ?
                     | ? U+3008 - U+3020 ?
                     | ? U+3030 ?
<operator-continue> := <operator-head>
                     | ? U+0300 - U+036F ?
                     | ? U+1DC0 - U+1DDF ?
                     | ? U+20D0 - U+20FF ?
                     | ? U+FE00 - U+FE0F ?
                     | ? U+FE20 - U+FE2F ?
                     | ? U+E0100 - U+E01EF ?
```

## Assginment operators [↵](#operators)

```
<assign-op> := <basic-assign-op> | <compound-assign-op>
<basic-assign-op> := '='
<compound-assign-op> := <infix-op> '='
```

Assignment operators are infix operators.

Assignment operators moves a value into a specific place, or modifies a value.

The left-hand operand of hte assignment operator must be an assignment expression, in the most simple case, the aasignee is a simple place expression and the below specificiation assumes this ito simplify the explenation.

An assignment expression uses the following terms in its expression:
```
'assignee' = 'assigned value'
```

### Basic assignment [↵](#assginment-operators-)

Evaluating assignment expressions begins by evaluating its operands.
This assigned value will be evaluated first, followed by the assginee expression.

> _Note_: Unlike other expressions, the right-hand operand is evaluated before the left hand expression

Before assignment, the assignment will first drop the current value of hte assigned place, unless the place is an uninitialized value.
Next, it will either copy or move the assigned value in the location of hte assignee.

### Destructuring assignment [↵](#assginment-operators-)

Destructuring assignment is a counterpart to destructuring patterns for variable declarations, permitting assignment of complex values such as tuples and structures.

In contrast to destructuring declarations using `let`, patterns may not appear on the left-hand side of an assignment due to syntactical ambiguities.
Instead a group of expressions are designated to be [assignee expressions], and are permitted on the left-hand side of an assignment.
Assignee expressions are then desugared to pattern matches followed by subsequent assignments.
The desugared patterns must be irrifutable: in particular, this means that only slice pattens whose lenght is known at compile time, and the trivial slice `[..]` are permitted during structuring assignment.

Underscore experssions and empty range expressions may be used to ignore certain values, without binding them.

### Compound assignment [↵](#assginment-operators-)

Compound assignment expressions combine infix operators with assignment expressions.

The operator used for a compound assignment always ends on a '=', which is used to indicate assignment expressions.

Unlike other assginee operands, the assginee operand must be a place experession.

If both types are primitives, the modifying operand will be evaluated first, followed by the assignee.
It will then set the value of the assignee to the value of perfroming the operation of the operator on the values of hte assignee and modifying operand, and then assign it to the assignee.

> _Note_: Unlike other experssions, the right-hand operand is evaluated before the left-hand operand
> With the exception of a lazy operator, as the right-hand operand will just be passed as a closure, meaning it will be executed after the left-hand operand.

Othewise, the expression is syntactic sugar for calling a function of the overloaded compound assignment operator.
A mutable borrow to the assignee is automatically taken.

## Operator scoping and use [↵](#assginment-operators-)

```
<operator-use> := 'op' 'use' <use-root> [ '.' '{' <name> { ',' <name> }* [ ',' ] '}' ]
```

Operators have some special scoping rules, as they are not scoped relative to the module that contains them, but they are exclusivly at the global scope.
Only the actual operator set will be at a global level, but their respective traits will still be scoped like any other symbol.

Operators are imported using an `op use`, this will import all or specific operator sets from a given library into the global scope.
An imported operator set will always import all of its operators.

If any of the imported operator set would result in a duplicate operator, defined by it's punctuation and operator type, it will result in an error.

Unlike importing their associated traits, `op use`s are required to be within the main file of the library, i.e. in either the `main.mn` or `lib.mn` root, and must not be nested within a module in that file. 
One of the main purposes of this rule is to keep a consistent meaning of operators accross a library, i.e. avoiding a situation where an operator in 1 file has a different meaning than in another file, even if both are in the same library.

The core operators will be included by default via the core prelude.



[assignee expressions]: ./expressions.md#assign-expressions-
[lazily parameter]:     ./items/functions.md#lazy-parameters-
[parameter pack]:       ./generics.md#parameter-packs-