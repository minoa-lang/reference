# Wildcard patterns
```
<wildcard-patter> := '_'
```

A wildcard pattern is used to match any value, meaning that it is used to ignore certain values.
A wildcard matches exactly 1 pattern, as opposed to [rest pattern] (`..`), which matches to all remaining values/

A wildcard does not copy, move, or borrow the values it matches.

A wildcard is always irrifutable

> _Example_
> ```
> // always matches, and ignore the value `2`
> let (a, _) = (1, 2);
> 
> // ignore a function/closur parameter
> real_part := fn{ (a: i32, _: i32) => a };
> 
> // ignore a field in a struct
> let RGBA{ r, g, b, a: _ } = color;
> ```



[rest pattern]: ./reference-patterns.md