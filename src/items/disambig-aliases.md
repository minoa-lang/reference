# Disambiguation aliases
```
<disambig-alias> := 'alias' 'use' <name> '=' <trait-path> '.' <ext-name> ';'
```

A disambiguation alias allows an item to define a name which when used, will result in a [trait disambiguation] being used at that location.


> _Example_
> ```
> impl Foo as Bar {
>     alias use baz = Baz.bar;
> 
>     fn bar() {
>         // calls `self.(Baz.bar)`
>         self.baz();
>     }
> }
> ```

> _Example_
> 
> When a type implements 2 different traits with a function that has the same name and matching signature, a disabiguation syntex allows for a more concise use
> ```
> impl[T is Foo & Bar] T as Baz {
>     alias use foo_fn = Foo.func;
>     alias use bar_fn = Bar.func;
> 
>     fn(&self) baz() {
>         self.foo_fn();
>         self.bar_fn();
> 
>         // identical to
>         self.(Foo.func)();
>         self.(Bar.func)();
>     }
> }
> ```


[trait disambiguation]: ../identifiers-paths.md#trait-disambiguation-