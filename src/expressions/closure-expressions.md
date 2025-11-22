# Closure expressions
```
<closure-expr> := [ 'async' ] [ 'gen' ] [ 'move' ] 'fn' <closure-body>
<closure-body> := '{' [ [ <capture-list> ] [ <closure-params> ] [ '->' <type> ] => { <stmt>* } <expr> ] '}'
```

A closure expression, also known as a lamda or functor expressions in some other langauges, denotes a function that maps a list of parameters onto an expression which will run when called.
In addition, unlike a function, closures can capture values from the surrounding envirnment to be able to be used within the closure.

Each closure produces a unique, anonymous [closure type].

Closures may also be specified as being both/either async and/or generators.

## Closure parameters [↵](#closure-expressions)
```
<closure-params>        := <simple-closure-params> | <full-closure-params>
<simple-closure-params> := <name> { ',' <name> } [ ',' ]
<full-closure-params>   := '(' <fn-param> { ',' <fn-param> } [','] ')'
```

Closures may take in parameters in couple different ways:
- as simple list of names, denoting the parameters. However these have the following limiations:
  - the parameters' types must be able to be inferred from the surrounding context
  - they cannot have any attributes defined on them, nor any specifiers controlling how the parametes will be taken in
- as a full set of parameters within parentheses, these are similar to [function parameters] and use their grammar, but they do not support any labels.
- as implicit parameters defined below

If a simple list of names is used, this closure's parameters will take on the types of it's first usage.
When this closure is then used in subsequent locations, it will have already be typed with these inferred types, and will thus error if other types are passed

> _Example_
> ```
> // the closure will infer the types of `a` and `b` from their usage site
> a := fn{ a, b => a + b };
> 
> // `a` is inferred to use `i32`
> val := a(1, 2);
> 
> // error: closure parameter types mismatch, expected 'i32', found 'f64'
> val := a(1.0, 2.0);
> 
> // closure with full parameters, which makes the closure fully typed
> b := fn { (a: i32, b: i32) => a + b };
> ```

### implicit parameters [↵](#closure-parameters-)

When a closure can infer the parameters it will receive, no explicit parameters need to be defined.
These paramets can be accessed using the implicitly provided `$n` values, where `n` is the index of the parameter.

> _Example_
> ```
> fn foo(closure: fn(i32, i32) -> bool);
> 
> // closure can infer the parameters `$0` and `$1` from it's call-site
> foo(fn{ $0 < &1 });
> ```

## Closure captures [↵](#closure-expressions)

```
<capture-list>           := '[' <closure-capture> { ',' <closure-capture> }* [ ',' ] ']'
<closure-capture>        := <closure-common-capture> | <closure-var-capture>
<closure-common-capture> := '&' [ 'mut' ]
<closure-var-cpature>    := [ '&' [ 'unique' | 'mut' ] ] <name> [ '=' <path> ]
```

By default, a closure will capture a value using the [capture modes] defined in the [closure type].

Closures can however also specify explicitly how to capture variables.
This can be done either as a common option for all variables captures, or individually.

When a common capture mode is defined, this will override the default capture behavior of a closure type.
Otherwise, if only explicit variables ar ecaptures, all other values being captures will still use the default [capture modes].

These may also be combined, where all variables that have an explicit capture that is defined will use that, and all other values will use the common capture.

In addition, a value may also be provided an alias, by using a path-assigned capture, which will associate the captured path to the provided name.

To capture all variables by move, the `fn` may be preceded with a `move`.

When all values are captured by `move`, this means that the closure is independent from all lifetimes of those variable, and can be used in a location where using those variables would generally be illegal.

> _Example_
> ```
> a := 3;
> b := 4;
> 
> // uses default capture modes
> c0 := fn{ a + b };
> 
> // captures both values by reference
> c1 := fn{ [&] => a + b };
> 
> // explicitly captures `a` as move and `b` by an immutable borrow
> c2 := fn{ [a, &b] => a + b };
> 
> // set default capture to always capture by immutable borrow, and explicitly capture `a` by move
> c3 := fn{ [&, a] => a + b };
> 
> // caputes a by unique borrow, and b by mutable borrow
> c4 := fn { [&unique a, &mut b] => a + b };
> ```

## Closure trait implementations [↵](#closure-expressions)

The traits implemented by the resulting closure type depend both on the specifiers applied to the closure, and how variables are captured.
More information about this can be found [here].

## Async closures [↵](#closure-expressions)

An `async` closure is the closure's variant of an [`async` function] and works similarly to it.
When it comes to capturing variables, it follows the same behavior as capturing variables whithin an [`async` block].

Async closures also introduce a new async context.

## Operator methods [↵](#closure-expressions)
```
<op-method-expr> := <operator> [ '$' ]
                  | '$' <operator>
```

An operator method is a special syntax which allows the creation of an closure applying an operator on 2 types, without requiring an explicit closure to be passed.
How this operator is passed, depends on it's kind.
- infix operator are supplied as-is, this is how operator methods are interpreted by default
- pre- and postfix operators require an additional indication by appending `$` before or after it, respectively.

> _Example_
> ```
> fn foo(_ closure: fn(i32, i32) -> i32) { ... }
> 
> foo(+);
> // is equivalent to
> foo(fn{ a, b => a + b });
> 
> fn bar(_ closure: fn(i32) -> i32) { ... }
> 
> bar(-$);
> // is equivalent to
> bar(fn{ a => -a });
> ```


[`async` block]:       ./block-expressions.md#capture-modes-
[`async` function]:    ../items/functions.md#async-functions-
[function parameters]: ../items/functions.md#parameters-
[closure type]:        ../type-system/types/function-like-types/closure-types.md
[capture modes]:       ../type-system/types/function-like-types/closure-types.md#capture-modes-
[here]:                ../type-system/types/function-like-types/closure-types.md#call-traits-and-coercions-