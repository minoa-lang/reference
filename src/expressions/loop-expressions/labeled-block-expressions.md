# Labeled block expressions
```
<labeled-block-expr> := <label> ':' [ 'const' ] <block>
```

A labeled block is a variant of a [block expressions] which supports exiting expressions.
Unlike loops, a labeled block is required to have a label, as it will otherwise be interpreted as a regular block expression.

The block is limited to only regular and [`const` blocks].


_Example_
```
:foo: {
    if a == b {
        break :foo;
    }

    break;
}
```

[block expressions]: ../block-expressions.md
[`const` blocks]:   ../block-expressions.md#const-block-