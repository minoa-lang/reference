# Extensions
```
<extension-item> := { <attribute> }* [ <vis> ] 'extension' <name> '{' { <ext-items> }* '}'
<ext-items>      := <use-item>
                  | <impl-item>
                  | <when-item>
```

Extensions allows for functionality to be added to an extern type, including allowing external traits to be implemented on these items, which would otherwise not be coherent.
Extensions may **not** contain any coherent implementations, if a coherent implementation needs to exists, this should be done via a feature on the library.

Each extension has a name which is used for an [extension `use` item], when imported, they are available throughout the entire library.

Extensions can contain muliple implementations, which will all be imported when the extension is imported.

When importing an extension, it may conflict with another implementation in one of the following cases:
- the item already had a matching implementation in the library where the type or trait was already defined
- 2 or more extension define a matching implementation

If a conflict exists after importing the extensions, it will result in an error.

> _Note_: For a more explicit version on this, which requires explicit casting, see [adapt type aliases]

> _Example_
> ```
> extension FooExt {
>     // Add functionality to `i32`
>     impl i32 {
>         fn(&self) fooify() -> Foo { ... }
>     }
>     
>     // Implement an external trait on `i32`
>     use ext:lib.Trait;
>     impl i32 as Trait {
>     }
> }
> ```

> _Example_ Coherent implementations in an extension
> ```
> trait Bar;
> 
> struct Foo;
> 
> extension Coherent {
>     // error: Cannot implement a coherent implementation within an extension
>     impl Foo as Bar {}
> }
> ```


[extension `use` item]: ./use.md
[adapt type aliases]:   ./type-aliases.md#adapt-type-alias-