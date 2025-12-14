# Rest patterns
```
<rest-pattern> := '..' [ [ 'ref' ] [ 'mut' ] <name> ]
```

A rest pattern is a variable lenght wildcard pattern which matches 0 or more elements that haven't been matched yet directly before or after, and ignores their value.

They are limited to the following patterns:
- [tuple patterns]
- [slice pattern]
- [tuple struct patterns]

> _Note_: The rest pattern should not be confused with a [struct rest pattern]

A rest pattern is always irrifutable.

> _Example_
> ```
> match slice {
>     [] => println("None"),
>     [one] => println("single element: \{one}"),
>     [head, ..] => println("Multiple values with head: \{head}"),
> }
> 
> match slice {
>     // match anything that ends on a '!'
>     [.., '!'] => println("!"),
> 
>     // match anything that contains an 'a'
>     [.., '\0', ..] => println("contains 'a'"),
> 
>     // matches both the entire slice, and the last element of the slice
>     whole @ [.., last] => println("whole arr: \{whole}, last elem: \{last}")
> }
> 
> match tuple {
>     (.., z) => println("Z-coord: \{z}"),
>     (42, ..) => println("Starts with 42"),
>     (..)     => println("Anything else"),
> }
> ```

# Combined rest and identifier patterns [↵](#rest-patterns)

In addition to a regular rest pattern, the pattern also allows for a shorter way of writing an [identifier pattern] which has a rest pattern as its associated pattern.

> _Note_: The provided name may not conflict with a constant's name, as this would be interpreted as a [range pattern].

> _Example_
> ```
> a := [1, 2, 3];
> 
> match a {
>     [head, ..tail] => (),
> }
> ```
> is equivalent to
> ```
> match a {
>     [head, tail @ ..] => (),
> }
> ```



[identifier pattern]:    ./identifier-patterns.md
[range pattern]:         ./range-patterns.md
[array patterns]:        ./slice-patterns.md
[struct rest pattern]:   ./struct-patterns.md#struct-rest-pattern-
[tuple patterns]:        ./tuple-patterns.md
[tuple struct patterns]: ./tuple-struct-patterns.md