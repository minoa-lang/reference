# Tuple patterns
```
<tuple-pattern>       := '(' <tuple-pattern-elems> ')'
<tuple-pattern-elems> := <pattern> ','
                       | <rest-pattern>
                       | <tuple-pattern-elem> { ',' <tuple-pattern-elem> }* [ ',' ]
<tuple-pattern-elem>  := { <attribute> }* [ <int-literal> ':' ] <pattern>
                       | { <attribute> }* <ext-name> ':' <pattern>
```

Tuple patterns match any [tuple] value that also matches the criteria defined in the subpatterns.
This allows for the value to be destructured into its elements.

Tuple pattern elements may also specify the specific element they are matching, either by using the elements tuple index, or using its name, in case of a [named tuple].
This also allows fields to be specified out of order.
Any field that come after such a field, but does not have the element they represent specified, will be interpreted as the fields that comes directly after it in the tuple.

When matching a 1-ary tuple, i.e. a tuple with only a single element, and the field is not explicitly specified, the pattern must be followed by a `,`.
If this `,` is not present, this will result in a [grouped pattern] instead of a tuple pattern.

A tuple containing only a [rest pattern], i.e. `(..)`, may be specified without a trailing comma.
This variant is special form of a tuple pattern which matches any tuple.
This variant is always irrifutable.

Only a single [rest pattern] is allowed within a tuple pattern, unless they are separated by at least 1 tuple pattern element which specified the element they are matching.

A tuple pattern is rufutable, if any of its subpatterns is refutable.

> _Example_
> ```
> match (2, 3) {
>     (1, 2) => (),
>     (2, _) => (),
>     (..)   => (),
> }
> 
> match tuple {
>     (1, "hello")       => (),
>     (1: "hello", 0: 2) => (),
> }
> 
> match named_tuple {
>     // match using field names
>     (int: 1, string: "hello") => (),
>     // match using field indices
>     (0: 1, 1: "hello")        => (),
>     // match as any other tuple
>     (1, "hello")              => (),
> }
> 
> match value {
>     // error: cannot have 2 rest patterns without a pattern explicitly refering to field seperating them
>     // (.., 1, ..)  => (),
> 
>     // allowed, as we are now checking for a very specific field in the order
>     (.., 2: 10, ..) => (),
> }
> ```



[grouped pattern]: ./grouped-patterns.md
[rest pattern]:    ./rest-patterns.md
[tuple]:           ../type-system/types/composite-types/tuple-types.md
[named tuple]:     ../type-system/types/composite-types/tuple-types.md#named-tuples-