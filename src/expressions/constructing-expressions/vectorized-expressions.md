# Vectorized expression
```
<vectorized-expr> := <vectorized-list-expr>
                   | <vectorized-count-expr>
                   | <vectorized-comprehension-expr>
```

Vectorized expressions are very similar to an [array expression], with the main difference that is creates a [vectorized type].
One major difference is that expression cannot be declared out of order and the type of each element is restricted to the element types supported by the current vectorized type.

If not enough values are provided to where the size of the vectorized expression is not a multiple of the vectorized type's lanes, the vectorized type may pad this with elements of the value `T.(Default.default)()`.

> _Note_: Most of the definition is this file is verbatim of what is found for an [array expression], with references to arrays changed to vectorized, and unsupported features removed.

## Vectorized list expression [↵](#vectorized-expression)
```
<vectorized-list-expr> := '[<' <expr> { ',' <expr> }* [ ',' ] '>]'
```

A vectorized list is a form that lists out all values in the array, represented as a comma-separated list.
Each expression is evaluated from left to right, i.e. in the order they are written.

If the array list expression is provided with only a single expression, which is in the form of a loop, it will be interpreted as a [loop list comprehension].

> _Example_
> ```
> // single element array
> a := [<0>];
> 
> // multi element array
> b := [<1, 2, 3>];
> ```

## Vectorized count expression [↵](#vectorized-expression)
```
<vectorized-count-expr> := '[<' <expr> ';' <expr> '>]'
```

An vectorized count expression creates an array of a single value, with a number of elements in it.

How the array is created is defined by 2 expression, separated by a ';'
- the first, known as the 'repeat', represents the value which will be replicated across all values in the array
- the second, known as the 'count', represents the number of elements within the array, which must be a compile-time value, and must have type `usize`

The expression will copy the 'repeat' value 'count' times, if 'count' is a value greater than 1, the repeat value is therefore expected to be either:
- a value with a type implementing `Copy`
- a compile-time expression

> _Example_
> ```
> val := 42;
> a := [<val; 8>];
> ```

## Vectorized comprehension expression [↵](#vectorized-expression)
```
<vectorized-comprehension-expr> := '[<' <list-comprehension> '>]'
```

An vectorized comprehension expression creates an array based on a given [list comprehension] expression provided to it.
Unlike an array expression, the list comprehension does not have access to an `&self` value.
The provided list comprehension must be a compile-time expression.

> _Example_
> ```
> // simple list comprehension
> a := [< num for num in 0..=3 >];
> // will produce
> a := [< 0, 1, 2, 3 >];
> 
> // list comprehension with an expression
> b := [< num * 2 for num in 0..=3 >];
> // will produce
> b := [< 0, 2, 4, 6 >];
> 
> // list comprehension with a condition
> c := [< num for num in 0..=3 if num & 1 == 0 >];
> // will produce
> c := [< 0, 2 >];
> 
> // in a case where `opt_iter` causes `val` to be of type `T`.
> a := [< val for val in opt_iter if val? >];
> 
> // this would be equivalent to
> a := [< for val in opt_iter {
>     if val? {
>         yield val;
>     }
> } >];
> 
> // with a specific location
> a := [< 3 - i: i for i in 0..=3 >];
> // will result in
> a := [< 3, 2, 1, 0 >];
> 
> // loop list comprehension
> a := [< for i in 0..=10 {
>     // add even numbers to the array
>     if i & 1 {
>         yield i;
>     }
> 
>     // early out of the iteration
>     if i == 7 {
>         return i;
>     }
> } >];
> // will produce
> a := [< 0, 2, 4, 6 >];
> ```


[array expression]: ./array-expressions.md
[vectorized type]:  ../../type-system/types/abstract-types/vectorized-types.md