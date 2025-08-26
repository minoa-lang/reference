# Type coercions

Type coercions are implicit operations that change the type of a value.
They happen automatically at specific locations and are highly restricted in what types are allowed to coerce.

Any conversions allowed by coercion can als obe explicitly performed using the type cast operator `as`.

> _Note_: This description is informal and not yet fully defined, and should be more specific

## Coercion sites [↵](#type-coercions)

A coersion can only occur at certain sites in a program; these are typically places weherere the desired type is explicit or can be derived from explicit types (without type interference).
Possible coercion sites are:
- variable declarations where an explicit type is given.
- `static` and `const` item declarations (similar to variable declarations)
- Arguments to function calls
- Default paramter values for functions
- Instantiations of struct, unions, records, and enum and enum record variants fields
- Default field values
- Function results - either the final line of a block if it is not semi-color-terminated, or any expression in a `return` statement

If the expressions in one of these coercion sites is a ceorcion-propagating expression, then the relevant sub-expressions in that expression are also coercion sites.
Propagation recurses from these new coercion sites.
Propagating epxresson and their relevant sub-expressions are the following:
- Array literals, where the array has type `[n]T`.
  Each sub-expression in the array literal corecion sites for coercion to type `T`.
- Array literals with a repeating syntax, where the array has type `[n]T`.
  The repeating sub-expression is a coercion site for a coercion to type `T`.
- Tuples, where a tuple is a coercion site of type `(T0, T1, ..., Tn)`.
  Each sub-exprssion is a coercion site to the respective type, e.g. the 0th sub-expression is a coercion site to type `T0`.
- Parenthesized sub-expressions ( `(e)` ): if the sub-expression has type `T`, then the sub-expression is a coercion site to `T`.
- Blocks: if a block has type `T`, the the last expression (if it is not semicolon terminated) is a coercion site to `T`.
  This includes blocks which are part of control flow statements, such as `if`/`else`, if the block has a known type.


## Coecion types [↵](#type-coercions)

Coercions are allowed betweeen the following types:
- `T1` to `T3`, if `T1` coerces to `T2` and `T2` coerces to `T3`
- `&mut T` to `&T`
- `*mut T` to `*T`
- `[*]mut T` to `[*]T`
- `[*;x]mut T` to `[*;x]T`
- `&T` to `*const T`
- `&mut T` to `*mut T`
- `&T` or `&mut T` to `&U`, if `T` implements `Deref<Target = U>`
- `^mut T` to `&mut U`, if `T` implements `DerefMut<Target = U>`
- Function item types to `fn` pointers
- Non capturing closures to `fn` pointers
- `!` to any `T`

> _NOTE_: Since coercion are not anywhere close to being finalized, this list is incomplete

## Unsized coercions [↵](#type-coercions)

The following coercions arr called `unsized coercions`, since they relate to conversting sized types, and are permitted in a few cases where other coercions are not, as described above.
They can still happen anywhere a coercion can be done.

Two traits `Unsize` and `CoerceUnsized`, are used to assigst in this process and expose it for library use.
The following coercions are built-in and if `T` can coerce to `U` with one of them, than an implementation for `Unsize<U>` will be provide:
- `[n]T` to `[]T`
- `T` to `dyn U`, when T implements `U & Sized` and `U` is object safe.
- `Foo<..., T, ...>` to `Foo<..., U, ...>` when:
    - `Foo` is a struct
    - `T` implements `Unsize<U>`
    - The last field of `Foo` has a type involving `T`
    - If that field has type `Bar<..., T, ...>`, then `Bar<..., U, ...>` implements `Unsize<Bar<..., U, ...>>`
    - `T` is not part of hte type of any other fields

Additionally, a type `Foo<T>` can implement `CoerceUnsized<Foo<U>>` when `T` implements `Unsize<U>` or `CoerceUnsized<U>`.
This allows it to provide an unsized coercion to `Foo<T>`

> _NOTE_: Since coercion are not anywhere close to being finalized, this is incomplete

## Least upper bound coercions [↵](#type-coercions)

In some contexts, the compiler must coerce together multiple types to try and find the most general type.
This is called a "Least Upper Bound" coercion, or LUB coercions in short.
A LUB coercion is used and only used in the following situations:
- To find the common type for a series of `if` branches
- To find the common type for a series of `match` arms
- To find the common type between array elements
- To find the type for the return type of a closure with multiple return statements
- To check the type for the return tpe of a function with multiple return statements.

In each such case, there are a set of types `T0..Tn` to be mutually coerced to target type `Tt`, which is unknonw at the start.
Computing the LUB coercion is done iteratively.
The target type `Tt` begins as `T0`.
For each new type `Ti`, we cosider:
- If `Ti` can be coerced to the current target type `Tt`, then no change is made.
- Otherwise, check whether `Tt` can be coerced to `Ti`; if so, then `Tt` is changed to `Ti`.
  (this check is also conditional on whether all of the source expressions cosidered ths far have implicit coercions).
- If not, try to compute a mutual supertype of `Tt` and `Ti`, which will become the new target type.

If this fails, it will result in a compiler error.

