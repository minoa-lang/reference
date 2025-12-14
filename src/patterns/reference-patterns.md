# Reference patterns
```
<reference-pattern> := '&' [ 'mut' ] <pattern-allow-ref>
```

Refernce patterns allow a pattern to dereference pointers and references, and thus borrowing them.
This allows the underlying value to be used by non-reference binding modes.

Matching a value of type `&T` to a pattern `&val`, is equivalent to first dereferencing the value and then matching it.

By adding `mut`, it is possible to dereference any mutable reference or pointer.

Reference patterns are always irrifutable

> _Example_
> ```
> match *int_ref {
>     0 => "zero",
>     _ => "some",
> }
> 
> match int_ref {
>     &0 => "zero",
>     _  => "some"
> }
> ```