# Closure expressions
```
<closure-expr>            := [ 'unsafe' ] [ 'async' ] 'fn' '{' [ [ <capture-list> ] [ <closure-params> ] [ '->' <type> ] '=>' ] { <stmt> }* <expr> '}'

<closure-params>          := <simple-closure-params> | <full-closure-params>
<simple-closure-params>   := <name> { ',' <name> } [ ',' ]
<full-closure-params>     := <capture-list> '(' <full-closure-param> { ',' <full-closure-param> } [ ',' ] ')'
<full-closure-param>      := { <attribute> }* <pattern-no-top-alt> [ ':' <type> ]

<closure-list>            := '[' <closure-capture> { ',' <closure-capture> }* [ ',' ] ']'
<closure-capture>         := <closure-common-capture> | <closure-var-capture>
<closure-common-capture>  := 'move' | ( '&' [ 'mut' ] )
<closure-var-capture>     := [  ( '&' [ 'unique' | 'mut' ] ) ] <name> [ '=' <path> ]

```
A closure expression, also known as a lambda or a functor in some langauges, denotes a function that maps a list of parameters onto an expression that follows them.
In additon, closure can capture values from the surrounding context and use them within the closure, this differentiates them from functions.

There are 2 ways of writing parameters for a closure:
- a simple list of names, which are untyped and cannot have any patterns
- a full list of parameters, which can: be typed, contain patterns, and have attributes applied to the parameters

When patterns are used for parameters, they are just like `let` bindings, meaning that they must be irrefutable patterns.

Each closure expression has a unique, anonymous type.

The main difference between functions and closures, is that closures may capture variables from the environment around it.

How variables are captures depends on which style of parameters is used, meaning that if a full list of parameters is names, a capture list may also be provided.
If no explicit captures are provided, or a captured variable is not included within the capture list, the captures variable follows the default capture behavior as defined in the [closure type capture modes].

Captures can be expressed per variable, or as a common capture mode.
Common capture modes override the default capture behavior and makes it so that all non-explicitly captured variables follow the mode defined by it.
In addition, variable specific captures may also be defined, allow the exact capture mode for a variable to be defined.

A capture can also be a path-assigned capture, allowing a path to be refered to as a name within the closure.

If all variables are captured by move, this means that the closure is independent from all lifetimes of those variables and can be used when the variables would normally cause the closure to be invalid.

Closures may also be marked as `unsafe`, this means that their body is an unsafe context, but does not make the closure itself `unsafe` to do operation on.
This is equivalent to `fn { unsafe { ... } }`

Closures can also be marked as `async`, this is similar to [`async` blocks], but allowing arguments to be passed.

## Shorthand arguments [↵](#closure-expressions)

A shorthand arguments allow a closure to be written without explicilty listing any variables, by simple leaving them out.
This also allows the programmer to directly write the closure as if it were a block.

Arguments can still be accessed as `$N`, where `N` represent the 'index' of the parameter.

## Closure trait implementations [↵](#closure-expressions)

Which trait a closure type implements depends on how variables are captured and the types of the captured expression.
See the call trait section for how and when a closure implements the respective trait.

## Operator methods [↵](#closure-expressions)
```
<op-method-expr> := <operator> [ '$' ]
                  | '$' <operator>
```

Any location that accepts a closure can also accepts so-called operator methods, allowing an operator to be passed.
Infix operators can be passed as-is, this is the default way these operators will be interpreted.
Pre- and postfix operators must be identified using a special `$` character, either before or after the operator, to indicate which type of operator should be used.

## Autoclosures [↵](#closure-expressions)

Autoclosures allow regular expressions to be passed to any parameter accepting a closure, which has the parameter marked as `@autoclosure`, otherwise the closure needs to be explicitly provided.
They will automatically be converted into a closure.
This is only allowed when the expression does not have any parameters.

> _Note_: This is essentially how [lazy parameters] are passed to a function

## Closure argument expressions [↵](#closure-expressions)
```
<closure-arg-expr> := '$' <int-dec-literal>
```

A closure argument expression is used to access unnamed closure arguments.




[`async` blocks]:             ./block-expressions.md#async-blocks-
[lazy parameters]:            ../items/functions.md#lazy-parameters-
[closure type capture modes]: ../type-system/types/closure-types.md#capture-modes-