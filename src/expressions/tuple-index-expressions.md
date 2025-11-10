# Tuple index expressions
```
<tuple-index-expr> := <expr> [ '?' ] '.' <int-decimal-literal>
```

A tuple index expression allows access to a field within a tuple type, using its index.

The index is an unsigned decimal literal containing no leading zeros, not underscores.
This is because the index is not used as an actual numeric value, but as the name the corresponding tuple field.

Evaluating a tuple index expressions has no side-effects, other than a check to the implementation of `OptAccess` for optional chaining tuple indexing.
The resulting expression is a place expression, so it evaluates to the location of the tuple field with the same name as the tuple index.

> _Note_: Named tuple fields can also be accessed using a [field access]

> _Example_
> ```
> a := (1, 2, 3);
> 
> assert(a.0 == 1);
> assert(a.1 == 2);
> assert(a.2 == 3);
> 
> // error: tuple index is out-of-bounds
> // a.3;
> 
> // error: a tuple index may not contain leading 0s
> //a.01;
> 
> // assuming the tuple contains index 10:
> // error: a tuple index may not contain any underscores
> // a.1_0;
> ```

## Optional chaining [↵](#tuple-index-expressions)

Similar to an [optional field access], the tuple idex expression supports optional chaining, also known as null-propagating tuple indexing.
This means that the tuple indexing expression only happens when the left-hand operand is a value that does not have an erronous value.

This operation is handled by the `OptAccess` trait and works on all types wrapping a tuple type.

> _Example_
> ```
> a: ?(i32, i32, i32) = (1, 2, 3);
> assert(a?.0 == 1);
> assert(a?.1 == 2);
> assert(a?.2 == 3);
> 
> b: ?(i32, i32, i32) = null;
> assert(a?.0 == null);
> ```



[field access]:          ./field-access-expressions.md
[optional field access]: ./field-access-expressions.md#optional-chaining- "Todo: section does not exists yet"