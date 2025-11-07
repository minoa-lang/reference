# Array expressions
```
<array-expr> := <array-list-expr>
              | <array-count-expr>
              | <array-comprehension-expr>
```

An array expression allows for the creation of a value with an [array type].

When creating a regular array, and when provided with an explicit location inside of an expression, the array will have a size of the highest index of elements provided by the expression + 1.
  
When creating an enumerated array, the array will always have the same number of values are variants in the provided enum.

When providing a location for any array, any indices that are not explicitly mentioned will be filled with the value produced by the  `T.(Default.default)()` implementaion.

## Array elements [↵](#array-expressions)
```
<array-elem> := [ <expr> ':' ] <expr>
```

Each element constist out of an expression, but may also be preceded by one of the following values:
- if the array is a regular array:
  - an index into the array to which the value should be assigned
  - a range, to which all indices will be assigned the provided values, this expression has the same limitation as those for a [array count expression].
- if the array is an [enumerated array], with a path to a unit enum variant

These are known as the location of the element.

## Array list expression [↵](#array-expressions)
```
<array-list-expr> := '[' <array-elem> { ',' <array-elem> }* [ ',' ] ']'
```

An array list is a form that lists out all values in the array, represented as a comma-separated list.
Each expression is evaluated from left to right, i.e. in the order they are written.

If the array list expression is provided with only a single expression, which is in the form of a loop, it will be interpreted as a [loop list comprehension].

> _Example_
> ```
> // single element array
> a := [0];
> 
> // multi element array
> b := [1, 2, 3];
> 
> // out of order multi element array
> c := [ 2: 0, 1: 3, 0: 6 ];
> ```

## Array count expression [↵](#array-expressions)
```
<array-count-expression> := '[' <expr> ';' <expr> ']'
```

An array count expression creates an array of a single value, with a number of elements in it.

How the array is created is defined by 2 expression, separated by a ';'
- the first, known as the 'repeat', represents the value which will be replicated across all values in the array
- the second, known as the 'count', represents the number of elements within the array, which must be a compile-time value, and must have type `usize`

The expression will copy the 'repeat' value 'count' times, if 'count' is a value greater than 1, the repeat value is therefore expected to be either:
- a value with a type implementing `Copy`
- a compile-time expression

> _Note_: Array count expressions do not support key-value pairs

> _Example_
> ```
> // Creates an array with 8 `42`s in it, as `i32` is a copy type
> val := 42;
> a := [val; 8];
> 
> // Creates an array with 4 `null` values, even though `NonCopy` is not `Copy`, the array is created using a constant expression.
> b: [?NonCopy] = [null; 4];
> 
> // Creates an array with a single value, while `NonCopy` is not `Copy`, it does not need to be copied, as the array will only contain a single element.
> val := NonCopy{};
> c := [val; 1];
> ```

## Array comprehension expression [↵](#array-expressions)
```
<array-comprehension-expr> := '[' <list-comprehension> ']'
```

An array comprehension expression creates an array based on a given [list comprehension] expression provided to it.
The list comprehension has access to an implicit `&self` value, which is of a slice type with the state of the array in the current state (which is accessible in a compile-time constant).
The provided list comprehension must be a compile-time expression.

> _Example
> ```
> // simple list comprehension
> a := [num for num in 0..=3];
> // will produce
> a := [0, 1, 2, 3];
> 
> // list comprehension with an expression
> b := [num * 2 for num in 0..=3];
> // will produce
> b := [0, 2, 4, 6];
> 
> // list comprehension with a condition
> c := [num for num in 0..=3 if num & 1 == 0];
> // will produce
> c := [0, 2];
> 
> // in a case where `opt_iter` causes `val` to be of type `T`.
> a := [val for val in opt_iter if val?];
> 
> // this would be equivalent to
> a := [for val in opt_iter {
>     if val? {
>         yield val;
>     }
> }];
> 
> // with a specific location
> a := [3 - i: i for i in 0..=3];
> // will result in
> a := [3, 2, 1, 0];
> 
> // loop list comprehension
> a := [for i in 0..=10 {
>     // add even numbers to the array
>     if i & 1 {
>         yield i;
>     }
> 
>     // early out of the iteration
>     if i == 7 {
>         return i;
>     }
> }];
> // will produce
> a := [0, 2, 4, 6];
> 
> // loop list comprehension using the implicit `self` value
> b := [while self.len() < 4 {
>     yield self.last() + 2;
> }];
> // will produce
> b := [0, 2, 4, 6];
> ```



[array count expression]: #array-count-expression-
[list comprehension]:     ../list-comprehension-expressions.md
[catch operator]:         ../../operators/special-operators.md#catch-operator-
[array type]:             ../../type-system/types/sequence-types/array-types.md
[enumerated array]:       ../../type-system/types/sequence-types/array-types.md#enumerated-arrays-
