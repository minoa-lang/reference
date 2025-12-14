# Type check patterns
```
<type-check-patter> := 'is' <type> [ '@' <pattern> ]
                     | 'is' <struct-pattern>
                     | 'is' <tuple-struct-pattern>
```

Type check pattern match any value that has a matching type.
It is also possible to provide a subpattern which also needs to be matched, for the pattern to result in a match

Type check patterns are mainly used in combination of:
- compile-time `type` parameters
- generic types
- [adhoc enum type]
- a value with a [DST type]

> _Example_
> ```
> match value {
>     is i32 => println("i32 value"),
>     is i64 => println("i64 value"),
>     _ => (),
> }
> ```

When the pattern being matched a against is either a [struct pattern] or a [tuple struct pattern], the pattern may be directly placed where otherwise the type would be expected.
This requires the pattern to have an explicit path, and **not** an inferred path.

> _Example_
> ```
> match value {
>     is Foo{ a, b } => (),
>     is Bar(a, b)   => (),
> }
> ```



[struct pattern]:       ./struct-patterns.md
[tuple struct pattern]: ./tuple-patterns.md
[adhoc enum type]:      ../type-system/types/composite-types/enum-types.md#adhoc-adt-enums-
[DST type]:             ../type-system/dynamically-sized-types.md