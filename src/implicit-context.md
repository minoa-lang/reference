# Implicit Context

All functions and methods have access to an implicit context.
This is a collection of values which gets passed impliclty whenever the [`minoa` ABI] is used.

The implicit context may carry both 'global' and thread local data.


All 'global' data within the context is immutable by default, and requires [interior mutability] to be modified, such as via a [`Mutex`].
This is done to ensure no data can be modified in a way which would result in concurency problems, i.e. it used simiar logic to why only a singular mutable reference is allowed.
Additionally, any data within the implicit context must adhere to the [`Sync`] trait.

Any thread local data may be mutable, as it is exclusive to each thread.
This data differs from a [`tls static`] in 2 ways:
- it provides access to any code which would otherwise not have visibility to the value
- it limits access to callsites directly called from `main` via only call with the [`minoa` ABI], and prevents access in case any other ABI boundary is crossed.

The context may be accessed using the `#ctx` meta-variable.

> _Example_
> ```
> struct Foo {}
> 
> // adds a context member with the name `foo` of type `Foo`
> #add_ctx_member(foo: Foo);
> 
> fn main() {
>     // initialize `foo`
>     #ctx.foo = Foo{};
> 
>     foo := &#ctx.foo;
> }
> ```

## Setting members [↵](#implicit-context)

Each member of the implicit context is lazily stored, meaning that it cannot be accessed before it is explicitly set from code.
Each member may only be assigned once, any subsequent assignments will result in a panic, unless explicitly dropped.

> _Example_
> ```
> // `#ctx.foo` does not exists yet at this point
> 
> // Setting `foo`
> #ctx.foo = Foo{};
> 
> // Setting `foo` once again results in a panic, as we can only set it once
> #ctx.foo = Foo{};
> ```

## Dropping members [↵](#implicit-context)

The implicit context is the only value which is implicitly dropped at the end of the program, without requiring the user to explicitly drop it.

Since it is not possible to statically infer the drop order for any members stored within the implicit context, as they may be provided from via external libraries, dropping values is handled dynamically via the implicit context's internal bookkeeping.
Whenever a member is assigned, the context registers it in its droplist, which will eventually be used to drop all members in reverse order.

It is additionally possible to explicitly drop a value within the context using the [`drop_from_ctx`], which in addition to dropping the member, also updates the internal state to skip dropping this value at the end of the program.
It is also possible to drop the entire context manually using [`drop_ctx`].

> _Warning_: Explicitly dropping the context or any member in it is `unsafe`, as it is up to the developer to guarantee that no other thread will still access the now dropped context or member.
>            It is therefore not recommended to manually do it if this can not be guaranteed.

If a member has been dropped, the member may be reassigned once more.

_Example_
```
// Takes a key-path to the member to drop, and updates the internal state of the implicit context
unsafe drop_ctx_member(\.foo);

// Drops the entirety of the current context
unsafe drop_ctx();
```

## Defining members [↵](#implicit-context)

It is possible for any artifact to define its own values into the implicit context.

Each member that is added to a context must be unique, meaning that if 2 different libraries were to define a member with the same name, it will result in a compiler error.

A member can be defined using the builtin [`#add_ctx_member`] and  meta-functions.

_Example_
```
```

## Accessing members [↵](#implicit-context)

Context members are exposed to the user as [properties], and are made available via both a reference getter and an optional reference getter.
While directly access on any of its member is allowed, this will result in a panic if the value has never been set.
This member can safely be accessed regardless of it being initialized using [optional chaining].

Additionally, `tls` members also provide access via mutable reference getters.

> _Example_
> ```
> fn foo() -> ?() {
>     // allocate memory via the context provided arena allocator.
>     // We access the arena via the optional reference getter
>     mem := #ctx.arena?.alloc(128).
> 
>     // We access the arena via the refernce getter, since if we get here, we know an arena is provided, as we would have bailed from the function earlier otherwise.
>     #ctx.arena.free(mem);
> }
> ```

It is also possible to use the fact that members have an optional reference getter to check if a member has an assigned value.
This is done by check if a member is set by comparing it to `null`.

> _Example_
> ```
> assert(#ctx.arena != null).
> 
> // The compiler may also use the above assert to optimize out any additional null checks when calling the member.
> // This however is based on the assumption that the context and member will not be dropped anywhere else in concurrent code.
> #ctx.arena.something();
> ```

> _Perf_: Since libraries cannot know ahead of time at where in the context the value will be stored, a call to the context will always result in both a function call and an indirection.
>         It is therefore recommended to hold a reference to the member in locations where the member will be accessed in a hot path, in cases where it can be guaranteed that the context or its member will not be dropped.



[`minoa` ABI]:           ./abi.md "Todo: link to correct section"
[optional chaining]:     ./expressions/field-access-expressions.md#optional-chaining-
[`tls static`]:          ./items/statics.md#thread-local-starage-
[properties]:            ./items/properties.md
[interior mutability]:   ./type-system/interior-mutability.md

[`Mutex`]:               #implicit-context "Todo: link to docs"
[`Sync`]:                #implicit-context "Todo: link to docs"
[`drop_from_ctx`]:       #dropping-members- "Todo: link to docs"
[`drop_ctx`]:            #dropping-members- "Todo: link to docs"
[`#add_ctx_member`]:     #defining-members- "Todo: link to docs"
[`#add_ctx_tls_member`]: #defining-members- "Todo: link to docs"