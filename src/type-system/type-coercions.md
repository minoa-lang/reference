# Type coercions

Type coercions are implcit operation that change the type of a value.
They happen automatically at specific location and are highly restricted in what types are allowed to coerse.

Any conversions allowed by coercion can also be explicitly performed using the type cast operator `as`.

> _Note_: This description is informal and not yet fully defined

## Coercion sites [↵](#type-coercions)

A coersion can only occur at certain sites in a program.
These are typically places where the desired type is explicit or can be derive from explicit types (without type inference).
Possible coersion sites are:
- variable declaration where an explicit type is given
  ```
  let a: &i32 = &mut 123;
  ``` 
- `static` and `const` item declarations (similar to variable declarations)
- Arguments to function calls, i.e. argument is coerced to type of parameter
  ```
  fn foo(_: &i32) {}
  
  foo(&mut 123);
  ```
  For methods called, the receiver is coerced differently, see [method call expressions] for more info. 
- instantiations of struct, unions, or enum variant fields
  ```
  struct Foo { x: &i32 }

  Foo { x: &mut 123 };
  ```
- Default field values
  ```
  struct Foo {
      x: &i32 = &mut 123,
  }
  ```
- Function results, either the final line of a block if not semi-colon terminated, or any expressions in a [`return`] or [`yield`] expression

If any expressions in one of these coercion sites is a coercion-propagating expression, then the relavent sub-expression in that expression are also coercion sites.
Propagation recurses from these new coercion sites.
Propagation expressions and their relativent sub-expressions are one of the following:

The following propagation expressions results in coercion sites for the given sub-expression to type `U` or its relative type `Un`.

- [array expressions] for an array of type `[N]U`:
  - each sub-expression in a list syntax
  - the repeating expression in a repeating syntax
  - in a comprehension syntax
    - the last expression in the outer loop, if not semi-colon terminated
    - [`yield`] expression
- [tuple expressions] for a tuple of type `(U0, U1, ..., Un)`: each sub-expression, i.e. the 0th element is coerced to `U0`, etc.
- [parenthesized expressions] of a type `U`: its sub-expression
- [block expressions] for a block of type `U`: any last non-semi-colon terminated experssion, including blocks that are part of other expressions

> _Todo_: Use simple variable declaration if this would change (DO NOT COMMIT LINE)

## Coecion types [↵](#type-coercions)

Coercions between the following types are allowed (`->` means "coerces to"):
- `T1` -> `T3`, if `T1` -> `T2` and `T2` -> `T3`
- `&mut T` -> `&T`
- `^mut T` -> `^T`
- `[^]mut T` -> `[^]T`
- `[^:N]mut T` -> `[^:N]T`
- `&T` or `&mut T` -> `&U`, if `T` implements `Deref(.Target = U)`
  ```
  struct CharWrapper {
      val: char
  
      impl as Deref {
          type Target = char;
  
          fn(&self) deref() -> &char {
              &self.val
          }
      }
  }
  
  fn foo(arg: &char) {}
  
  x := &mut CharWrapper { value: 'a' };
  foo(arg: x);
  ```
- `&mut T` -> `&mut U`, if `T` implements `DerefMut(.Target = U)`
- function item types -> `fn` pointers
- non-capturing closures -> `fn` pointers
- `!` to any `T`
- literal to any corresponding types:
  - `DecLiteral`, `BinLiteral`, `OctLiteral`, `HexLiteral` -> [integer type]
  - `DecFloatLiteral`, `HexFloatLiteral` -> [floating point type]
  - `CharLiteral` -> [character type]
  - `StringLiteral` -> [string slice type]

## Unsized coercions [↵](#type-coercions)

The following coercions are called _unsized coercions_, since they relate to converting sized types, and are permitted in a few caes wher other coercions are not, as described above.
They can happen anywhere a coercion can happen.

Two traits are used to assist in this process and expose it for library use: [`Unsize`] and [`CoerceUnsized`].
The following coercions are built-in, and if `T` can be coerced to `U` with one of them, that implementation for `Unsize<U>` will be provided:
- `[n]T` -> `[]T`
- `T` -> `dyn U`, when T implements `U & Sized`, and `U` is [object safe]
- `dyn T` -> `dyn U` when `U` is a supertrait of `T`:
  - allows dropping [marker traits], i.e. `dyn T & Auto` to `dyn U` is allowed
  - allows adding [marker traits] if the principal trait has the auto trait as a super trait, i.e. given `trait T: U + Auto {}`, `dyn T` to `dyn U + Auto` coercions are allowed.
- `Foo(..., T, ...)` -> `Foo(..., U, ...)` when:
  - `Foo` is a struct
  - `T` implements `Unsize(U)`
  - The last field of `Foo` has a type involving `T`
  - It that field has type `Bar(T)`, then `Bar(T)` implements `Unsize(Bar(U))`
  - `T` is not part of the type of any other fields.

In addition, a type `Foo(T)` can implement `CoerceUnsized(Foo(U))` when `T` implements `Usize(U)` or `CoerceUnsized(Foo(U))`.
This allows it to provide an unsized coercion to `Foo(U)`.

## Least upper bound coercions [↵](#type-coercions)

In some contexts, teh compiler must coerce together types to try and find the most general type.
This is called the _Least Upper Bound_ coercion or _LUB_ coercion is short.

A _LUB_ coercion is used and only used in the following situations:
- to find the common types for:
  - a series of [`if`] and [`loop`] expressions
  - a series of [`match`] arms
  - array elements
  - return type of a closure with multiple returns
  - return type of a function with multiple returns
  
In each case, there is a set `T0, ..., Tn`, which need to be mutually coerced into a target type `Tt`, which is unknown at the start.
Computing the LUB coercion is done in the following manner:
1) set `Tt` to `T0`
2) for each new type `Ti`, consider
  1) if `Ti` can be coerced other current target type `Tt`, then no change is made.
  2) otherwise, check wether `Tt` can be coerced to `Ti`, if so, set `Tt` to `Ti`.
     (This implicilty ensures that all of the previous types can also coerce to `Tt`)
  3) if not, try to compute a mutual supertype of `Tt` and `Ti`, which it will set `Tt` to.
3) If this fails, it will rsult in a compiler error.

> _Example_: `if` branches
> ```
> v := if true {
>     a
> } else if false {
>     b
> } else {
>     c
> };
> ```

> _Example_: `while`-`else`
> ```
> v := while false {
>     break a;
> } else {
>     b
> };
> ```

> _Example_: `for`-`else`
> ```
> v := for i in 0..3 {
>     break a;
> } else {
>     b
> };
> ```

> _Example_: `match` arms
> ```
> v := match 4 {
>     0 => a,
>     1 => b,
>     _ => c,
> };
> ```

> _Example_: closure with multiple returns
> ```
> clo := fn {
>     if false {
>         return a;
>     }
>     b
> };
> v := clo();
> ```

> _Example_: Function with multiple returns
> ```
> fn foo() {
>     if false {
>         return a;
>     }
>     b
> }
> ```




[`CoerceUnsized`]:           #unsized-coercions- "Todo: link to docs"
[`Unsize`]:                  #unsized-coercions- "Todo: link to docs"
[block expressions]:         ../expressions/block-expressions.md
[array expressions]:         ../expressions/constructing-expressions/array-expressions.md
[tuple expressions]:         ../expressions/constructing-expressions/tuple-expressions.md
[`if`]:                      ../expressions/if-expressions.md
[`loop`]:                    ../expressions/loop-expressions.md
[`match`]:                   ../expressions/match-expressions.md
[method call expressions]:   ../expressions/method-expressions.md
[parenthesized expressions]: ../expressions/paren-expressions.md
[`return`]:                  ../expressions/return-expressions.md
[`yield`]:                   ../expressions/yield-expressions.md
[object safe]:               ../items/traits.md#object-safety-
[marker traits]:             ../items/traits.md#marker-traits- "Todo: not a section yet"
[character type]:            ../type-system/types/builtin-types/character-types.md
[floating point type]:       ../type-system/types/builtin-types/floating-point-types.md
[integer type]:              ../type-system/types/builtin-types/integer-types.md
[string slice type]:         ../type-system/types/sequence-types/string-array-slice-types.md#string-slice