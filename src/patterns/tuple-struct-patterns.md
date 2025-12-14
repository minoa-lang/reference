# Tuple struct patterns
```
<tuple-struct-pattern> := <struct-pattern-path> '(' <tuple-pattern-elems> ')'
```

Tuple struct pattern match any [tuple struct] or tuple-like [enum] values that also matches the criteria defined in the subpatterns.
This allows for the value to be deconstructed into its members.

The pattern can either be provided with an explicit path, or a `.`, which will infer the type based on the value being matched against

Tuple struct pattern fields may also specify the specific element they are matching, either by using the elements tuple index, or using its name, in case of a named tuple stuct.
This also allows fields to be specified out of order.
Any field that come after such a field, but does not have the element they represent specified, will be interpreted as the fields that comes directly after it in the tuple struct.

A tuple containing only a [rest pattern], i.e. `(..)`, may be specified without a trailing comma.
This variant is special form of a tuple struct pattern which matches any tuple struct with the same type.
This variant is always irrifutable.

Only a single [rest pattern] is allowed within a tuple struct pattern, unless they are separated by at least 1 tuple struct pattern element which specified the element they are matching.

A tuple struct pattern is rufutable, if either the pattern refers to a varaint on an enum with 2 or more vairants, of if any of its field's subpatterns is refutable.

> _Example_
> ```
> match Foo(2, 3) {
>     Foo(1, 2) => (),
>     .(3, 4)   => (),
>     Foo(2, _) => (),
>     Foo(..)   => (),
> }
> 
> match tuple {
>     TupleStruct(1, "hello")       => (),
>     TupleStruct(1: "hello", 0: 2) => (),
> }
> 
> match named_tuple {
>     // match using field names
>     .(int: 1, string: "hello") => (),
>     // match using field indices
>     .(0: 1, 1: "hello")        => (),
>     // match as any other tuple
>     .(1, "hello")              => (),
> }
> 
> match value {
>     // error: cannot have 2 rest patterns without a pattern explicitly refering to field seperating them
>     // .(.., 1, ..)  => (),
> 
>     // allowed, as we are now checking for a very specific field in the order
>     .(.., 2: 10, ..) => (),
> }
> ```

> _Note_: Tuple struct can also be matched as if they were non-tuple structs, this is done by using the index of the fields, or their names (if the fields are named) within the struct pattern
> ```
> match named_tuple_struct {
>     .(int: 1, string: "hello") => (),
>     .(0: 1, 1: "hello")        => (),
>     .(1, "hello")              => (),
> }
> ```
> is equivalent to
> ```
> match named_tuple_struct {
>     .{ int: 1, string: "hello" } => (),
>     .{ 0: 1, 1: "hello" }        => (),
>     // no direct equivalent for `.(1, "hello")`
> }
> ```



[rest pattern]:    ./rest-patterns.md
[enum]:            ../type-system/types/composite-types/enum-types.md
[tuple struct]:    ../type-system/types/composite-types/tuple-struct-types.md
[tuple]:           ../type-system/types/composite-types/tuple-types.md