# Call expressions
```
<func-call> := <expr> <function-args>
<func-args> := [ '?' ] '(' <func-arg> { ',' <func-arg> }* [ ',' ] ')' [ <trailing-closure> ]
             | <trailing-closure>
<func-args> := [ <name> ':' ] <expr>
```

A call expessions calls a function.

The expression will complete when the function returns.
If the function return a value, this value will be returned, this function is therefore a place or value expression, depending on the returned value.

The function expression can be called if it follows either of the following cases
- The expression is of a function or function pointer type.
- The expression is of a value that implement one of the relevant function interfaces, being:
    - `Fn`
    - `FnMut`
    - `FnOnce`

If needed, an automatic borrow of the function expression is taken.

If a value passed to an argument matches the is a name matching that of the label, the label may be left out.

An argument can have an additional function argument label in case the function requires one.
Any default arguments do not need to be provided and will be evaluated after evaluating the supplied operands, in the order they were defined in the signature.

Arguments are evaluated in the order they are written. i.e. left-to-right.

Whenever the function arguments have a `?` between the name and them, this means that this is a call of an optional trait function, which only calls the function when it is not null, or otherwise return a `.None` value.
When the function is called, it will automatically be wrapped in a nullable type.
In a case where the function returns a nullable type by itself, it will not be wrapped, but instead will just propage the `.None` value.

> _Todo_: Check if the call interfaces are correct

> _Todo_: Can labeled arguments be put out of order?

## Calling methods as functions [↵](#call-expressions)

All methods can be called as if they were functions.
The reciever may be passed as:
- the first arguments
- an arguments with the `self` label

Using the `self` label to call the function is not required, and the value may be passed unlabeled.

This can be useful for avoiding the receiver's automatic dereferencing, for example when using smart pointer types.

Example:
```
struct Foo {
    fn(&self) bar();
}

foo := Foo{};

foo.bar();
// can also be called as
Foo.bar(foo);
```

## Trailing closure [↵](#call-expressions)
```
<trailing-closures> := <trailing-closure> { <name> ':' <trailing-closure> }*
<trailing-closure> := '{' [ [ <capture-list> ] [ <closure-params> ] [ '->' <type> ] '=>' ] { <stmt> }* <expr> '}'
```

A trailing closure expression is a special version of a closure syntax that allows the closure to be defined behind a function.
For a trailing closure to be allowed, the function being called must take closures as its final arguments.

If a function contains multiple trailing closure arguments, the call may have multiple trailing closures, where the first closure will be matched to the first closure parameter that is compatible with it.
Any closure before the matched closure can then not be assigned via a trailing closure.
Any closures after the matched closure may be passed with an argument label, followed by another trailing closure.
Unless the closures after the matching closure have a default value, they are required to have a value passed to them.

For example:
```
fn foo(a: ?fn() -> i32 = None, b: ?fn(i32) -> i32 = None, c: ?fn(i32) -> i32 = None);

foo { $0 } c: { $0 }; // Calls `foo(b: fn{$0}, c: fn{$0})`
foo { $0 } // Calls `foo(b: fn{$0})`
foo { 1 } // Calls `foo(a: fn{1});


fn bar(a: ?fn() -> i32 = None, b: ?fn(i32) -> i32 = None, c: ?fn(i32) -> i32);
// The following calls all cause errors:
bar { $0 }; // trailing closure passed to `b`, cannot find trailing closure for `c`
bar { 1 }; // Trailing closure passed to `a`, cannot find trailing closure for `c`
bar { 1 } { $0 }; // Found block after trailing closure, additional trailing closures need to marked using their labels
```


If the function only has closure arguments passed to it, the parentheses may be left out, meaning that:
```
fn foo<T>(by: T) where T: Fn(i32, i32) -> bool { .. }
```
may be called as
```
foo() { $0 > $1 }
```
or as
```
foo { $0 > $1 }
```
> _Note_: This feature is also used by [initializers]



[initializers]: ../items/initializers.md