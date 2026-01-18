# Conditional compilation attributes

These attributes provide additional control over the what will be included in the resulting compiled code

## `cfg` [↵](#conditional-compilation-attributes)

The `cfg` attribute decides, based on a given predicate, whether the fragment it is applied to will be included within the compilation.

This attribute provides similar control over compilation as any variant of a [`when` item], but unlike any `when` variant, it is limited to only use [supported configuration options].

The syntax of the predicate can be found in the [configuration options].

> _Example_
> ```
> // windows specific implementation
> @cfg(target_os == .windows)
> struct OsData {
> }
> 
> // posix compliant specific implemenation
> @cfg(target_os == .posix)
> struct OsData {
> }
> 
> 
> // implemenation which requires the 'foo' feature to be available
> @cfg(foo)
> struct Foo {}
> 
> // implemenation which requires both the 'foo' and 'bar' features to be available
> @cfg(foo, bar)
> struct Bar {}
> 
> // implemenation which requires either the 'foo' feature to not be available, or 'bar' and 'baz' features to be available
> @cfg(any(not(foo), all(bar, baz)))
> struct Bar {}
> 
> // implementation requires 32-bit x86
> @cfg(target_arch == .x86 && target_pointer_width == 32)
> ```

> _Note_: even when the fragment is currently not included, based on the configuration options, the fragment still needs to contain valid code.

## `cfg_attr` [↵](#conditional-compilation-attributes)

The `cfg_attr` is similar to the `cfg` attribute, except that it decides whether to apply an attribute to fragment it is applied to.

It takes a predicate as its first value, and all following arguments are attributes to apply.

> _Example_
> ```
> @cfg_attr(target_os == .windows, path = "./windows/imp.mn")
> @cfg_attr(target_os == .posix, path = "./posix/imp.mn")
> mod platform;
> ```



[supported configuration options]: ../configuration-options.md
[configuration options]:           ../configuration-options.md#predicate
[`when` item]:                     ../items/when-items.md