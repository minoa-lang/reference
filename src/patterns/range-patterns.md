# Range patterns
```
<range-pattern>           := <exclusive-range-pattern>
                           | <inclusive-range-pattern>
<exclusive-range-pattern> := [ <range-pattern-bound> ] '..' <range-pattern-bound>
                           | <range-pattern-bound> '..'
<inclusive-range-pattern> := [ <range-pattern-bound> ] '..=' <range-pattern-bound>
<range-pattern-bound>     := <literal-pattern>
                           | <path-pattern>
```

Range patterns match any values that are within the specified bound.

An _exlusive range_ pattern matches any values from the lower bound up to, but **not** including the upper bound.
This range has 2 variants:
- a _from range_ pattern, with only a lower bound: which matches any values that are greater or equal to the lower bound
- a _to range_ pattern, with only an upper bound: which matches any values that are less, but **not** equal, to the upper bound

An _inclusive range_ pattern matches any vvalues from the lower bound up to, and including, the upper bound.
This range only has a single variant: the _inclusive to range_ pattern, with only an upper bound, which matches any values that are less or equal to the upper bound.

The bounds of the range may be of any type which implements both the [`Identity`] and [`TotalOrd`] traits.
However, both bounds must be of the same type, and the lower bounds must **not** be greater (or equal for exlusive ranges) than the upper bound.

When a path is provided, it must refer to a 

By default, this pattern is reffutable.

There are some characteristics when using certain [builtin types]:
- when using [floating point types], neither bounds may have a value of `NaN`
- when using any fixed with primitive type, if the range spans the entirety of all possible values, e.g. `0..=255` for a `u8`, the pattern is irrifutable.
- when using a [character type], the pattern will be irrifutable if following ranges of values is covered:
  - `char7`: `'\x00'..='\x7F'`
  - `char8`: `'\x00:..='\xFF'`
  - `char16`: `'\u{0000}'..='\u{FFFF}'`
  - `char`, `char32`: `'u{0000}'..='\u{D7FF}'` and `'\u{E000}'..='\u{10FFF}'`

It is possible to add these characteristics to user-defined types using the [`RangePatternLimits`] trait

> _Todo_: `RangePatternLimits` should define either a `MIN` and `MAX` constant, or give some additional control to allow section that are not included, like the surrogates for `char`, 
>         including specific values that are not allowed, like `NaN` in floats

> _Example_
> ```
> is_ascii_letter := match ch {
>     'a'..='z' => true,
>     'A'..='Z' => true,
>     _         => false,e
> }
> 
> match uint {
>     0   => println("Zero"),
>     1.. => println("Positive number"),
> }
> 
> // using a path to a constant
> match num_bytes {
>     ..bytes.KILO => println("size < 1KB"),
>     bytes.KILO..bytes.MEGA => println("1KB < size < 1MB"),
>     bytes.MEGA..bytes.GIGA => pritnln("1MB < size < 1GB"),
>     bytes.GIGA => pritnln("1GB < size"),
> }
> ```


[slice pattern]:        ./slice-patterns.md
[constant pattern]:     ./path-patterns.md#constant-patterns
[`Identity`]:           ../operators/core-operators.md#identity
[`TotalOrd`]:           ../operators/core-operators.md#totalord
[builtin types]:        ../type-system/types/builtin-types.md
[floating point types]: ../type-system/types/builtin-types/floating-point-types.md
[character type]:       ../type-system/types/builtin-types/character-types.md

[`RangePatternLimits`]: #range-patterns "Todo: Link to docs"