# Key-path expressions
```
<key-path>           := '\' <path> '.' <key-path-member> { [ '?' | '!' ] <key-path-subscript> }*
                      | '\' <path> '.' 'self'
<key-path-member>    := <name>
                      | <int-dec-literal>
                      | '[' <expr> ']'
<key-path-subscript> := '.' <name>
                      | '.' <int-dec-literal>
                      | '[' <expr> ']'
```

A key-path represents a path that points to a member of a type, as either a named member, or a tuple index.
A key-path is a compile-time value of the builtin `KeyPath(Ty, Member)` type.
If the type the path is on can be inferred, the path may be an inferred path.

Multiple keypaths may be chained by a sequence of accesses, for example, the keypath `\Foo.bar.baz` refers to the inner `baz` value of `Foo` in the following code:
```
struct Foo {
    bar: struct {
        baz: i32 // <-
    }
}
```

This sequence of accesses may be any of the following:
- field access
- tuple access
- index

In addition to direct access, the following are also supported:
- `?`: Handles optional chaining within the path
- `!`: Unwraps the preceeding value before an access

The keypath may also refer to the value itself, this is done using a special `self` key-path.

Any expression in a keypath that cannot be collapsed to a compile-time value will only be exectuded when using the specific keypath, not when the keypath is created, meaning it captures whatever value it needs to execute.


By default, each type implements an index by key-path on itself, returning a reference to an `Any` value.

A key-path can also be implicitly converted to an autoclosure, where it accessed its element from its root type, i.e. `fn{ (value: Ty) => value.Member }`.

An example usecase of a key-path could be to notify a value change on a property to a generic listener.
