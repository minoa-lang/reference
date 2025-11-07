# Tuple expressions
```
<tuple-expr>     := '(' <tuple-elem> ( { ',' <tuple-elem> }+ | ',' ) ')'
<tuple-elem>     := [ <tuple-elem-idx> ':' ] <expr>
<tuple-elem-idx> := <tuple-idx> | <ext-name>
```

A tuple expression allows for the creation of a value with a [tuple type].

The content of the expressions consists out of 2 or more comma-separated expression, called operands.
The tuple may also contain only 1 operand to create a 1-ary tuple, but this value **must** be followed by a comma.
If only a single operand and no trailing comma is provided, this expression will be interpreted as a [parenthesized expression].
Similarly, if no operands are present, this will be interpreted as a [unit expression].

The number of operands within the tuple expression will define the arity of the resulting tuple value.
Each operands will be evaluated from left to right, i.e. in the order they are written.
Each operand will be asssigned to the field they represent within the expression, meaning the first operand will be assignedto field '0', the second to '1', etc.

Named tuple can be created by prefixing at least 1 operand with a name.

Tuple operands may also be explicitly declared with an tuple index, this does require that all following tuple operands also have an explicit index.
This allows for operands to be evaluated in a different order than they would without specifying an index.

If the tuple is not an operand to a [in-place operator], the resulting tuple will be a value expression.

> _Note_: As defined by the [tuple struct type], it can be coerced from a tuple expression.

> _Example_
> ```
> // 3-ary tuple value
> a := ("text", 1337, 3.14);
> 
> // 1-ary tuple value
> b := (42, );
> 
> // tuple can also have named fields
> c := (foo: 2, bar: 6, baz: 10);
> 
> // or fields can even be declared out of order using indices into the tuple
> d := (0, 2: 34, 1: 56);
> // this is equivalent to
> d := (0, 56, 34);
> ```



[parenthesized expression]: ../paren-expressions.md
[unit expression]:          ../unit-expressions.md
[in-place operator]:        ../../operators/special-operators.md#in-place-operator-
[tuple struct type]:        ../../type-system/types/composite-types/tuple-struct-types.md
[tuple type]:               ../../type-system/types/composite-types/tuple-types.md