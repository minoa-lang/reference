# Slice patterns
```
<slice-pattern> := '[' [ <pattern> { ',' <pattern> }* [ ',' ] ] ']'
                 | '[' '..' ']'
```

Slice pattens match any [array] or [slice] values that also match the cirteria defined in the subpatterns.

The form `[..]` is a special form of a slice pattern, which matches an array or slice of any size.
This pattern is always irrefutable.

A slice pattern is refutable if any of its elements are refutable.


> _Example_
> ```
> // fixed sized array
> arr := [1, 2, 3];
> match arr {
>     [1, _, _] => println("starts with a 1"),
>     [a, b, c] => println("contains [ \{a}, \{b}, \{c} ]"),
> }
> 
> // varaibly sized slice
> match slice {
>     // matches a slice with exactly 3 values, matching the literals
>     [1, 2, 3] => (),
>     // matches a slice with 1 more elements, starting with a 2,
>     [2, ..]   => (),
>     // matches all other slices
>     [..]      => (),
> }
> ```


[array]: ../type-system/types/sequence-types/array-types.md
[slice]: ../type-system/types/sequence-types/slice-types.md