# Loop expression
```
<inf-loop-expr> := [ <label> ':' ] 'loop' <block>
```

An infinite `loop` expressions repeats the execution of its body an infinite times, or until it is exited from within the loop.

An infinite loop which does not contain any expressions which can exit the loop, this will be a diverging expressions, which has a `!` ([never]) type.
If the loop contains a [`break`], the expressions will have the type of the value returned by the break.


> _Example_
> ```
> // will loop indefinitly
> loop {
>     println("hello");
> }
> 
> // will be exited by the break
> loop {
>     break;
> }
> 
> 
> // fill exit when hitting the nested inner break with the loops label
> :label: loop {
>     loop {
>         break :label;
>     }
> }
> ```



[`break`]: ../break-expressions.md
[never]:   ../../type-system/types/builtin-types/never-types.md