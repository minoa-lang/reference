# Key-path expressions
```
<key-path>          := '\' [ <key-path-root> ] { '.' <key-path-segment> [ <key-path-postfix> ] }*
                     | '\' [ <key-path-root> ] '.' 'self'
<key-path-root>     := <iden>
                     | '(' <type> ')'
<key-path-segment>  := <name> [ <fn-args> ]
                     | <int-dec-literal>
                     | '[' <expr> ']'
<key-path-postfix>  := '?'
                     | '!'
```

A key-path expression represents a path to a, possibly nested, member of a type, relative to that type.
The expressions produces a value of a [`KeyPath`] type.

The key-path expression starts with the type to which the path is relative to.
If the type to which the path is relative can be infered, it may be left out.

This is followed by one or more member accesses, exists out of one of the following segmemts:
- name of a member, e.g. `.field_name`
- a tuple index of a member, e.g. `.0`
- indexing into a value, e.g. `.[0]`
- a method call, e.g. `.method(...)`

In addition, each member may have one of the following postfixes:
- `?`: will handle accesses as if done by [optional chaining]
- `!`: will unwrap the value before accessing its member

> _Example_
> ```
> struct Foo {
>     bar: struct {
>         baz: i32,
>     }
> }
> 
> // Key-path refer to the inner `baz`
> \Foo.bar.baz;
> 
> // When the type can be infered, the following would result in the same key-path
> \.bar.baz
>
> struct Bar {
>     arr: [4]i32
> }
> 
> // Key-path refers to the 2nd element of `arr`
> \Bar.arr.[2];
> 
> struct Baz(i32, i32);
> 
> // Key-path refers to the 2nd field in the tuple struct
> \Baz.0
> 
> struct Quux {
>     fn(&self) value(loc: i32) -> i32;
> }
> 
> // Key-path to value returned by `value()`
> \Quux.value(loc: 3);
> ```

Additionally, if instead of any segment, `self` is uses, the path will retreive the value itself.
This is also known as the identity path.

> _Example_
> ```
> mut val: i32 = 3;
> 
> // equivalent to `val = 4;`
> val[key_path: \.self] = 4;
> ```

Each type can by default be indexed using a key-path, which will then return the value located at the path.

> _Example_
> ```
> struct Foo {
>     bar: struct {
>         baz: i32,
>     }
> }
> 
> // Keypath refer to the inner `baz`
> \Foo.bar.baz;
> 
> foo := Foo{ bar{ baz: 42 } };
> 
> baz := foo[key_path: \.bar.baz];
> ```

Key-paths can also be used where a closure would be expected, as long as it matches the following signature: `fn(Type) -> Member`, where `Type` is either the type or a reference to it, and `Member` is type of the field being accessed.

> _Example_
> ```
> struct Task {
>     desc:      &str,
>     completed: bool,
> }
> 
> arr := [ 
>     Task{ desc: "foo", completed: true }
>     Task{ desc: "bar", completed: false }
>     Task{ desc: "baz", completed: true }
> ];
> 
> descs := arr.filter(\.completed).map(\.desc);
> ```
> this would be equivalent to
> ```
> descs := desc.filter(fn{ $0.completed }).map(fn{ $0.desc  });
> ```  

When an expression is used within a key-path, the value of this expression will be captured by value, after being calculated when evaluating this expression.

> _Example_
> ```
> arr := [1, 2, 3, 4];
> 
> fn index() -> i32 {
>     println("calculating index");
>     2
> }
> 
> // Index is calculated only once, meaning "calculating index" only gets printed when evaluating this expression
> path := \([4]i32).[index()];
> 
> // neither will print anything to the terminal
> a := arr[key_path: path];
> b := arr[key_path: path];
> 
> 
> // this also means that in the following case, `index` is not references anytime later on
> mut index := 0;
> 
> path := \([4]i32).[index];
> 
> 
> c := arr[key_path: path];
> 
> // `index` is increased, but path uses the original value
> index += 1;
> d := arr[key_path: path];
> 
> assert(c == d);
> ```


[optional chaining]: ./field-access-expressions.md#optional-chaining-

[`KeyPath`]: #key-path-expressions "Todo: Link to docs"