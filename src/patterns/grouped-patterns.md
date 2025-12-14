# Grouped patterns
```
<grouped-pattern> := '(' <pattern> ')'
```

Group patterns match any values which matches the inner pattern.
They are used to explicitly control the precedence of coumpound patterns.

A grouped pattern is refutable if the inner pattern is refutable.


> _Note_: Even though `(..)` looks like a grouped pattern with a [rest pattern] within it, this will instead refer to a [tuple pattern].

> _Example_
> 
> The following code without a grouped pattern would imply a range from `&0` to `5`, which is not a valid range pattern.
> The grouped expression explicitly specifies that the [range pattern] as a whole is refered to by the [reference pattern]
> ```
> match int_ref => {
>     &(0..=5) => (),
> }
> ```


[range pattern]:     ./range-patterns.md
[reference pattern]: ./reference-patterns.md
[rest pattern]:      ./rest-patterns.md
[tuple pattern]:     ./tuple-patterns.md