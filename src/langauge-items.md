# Language items

A language builtin, also known as a langauge item, are special items which have additional meaning which the compiler understands.
All of these types are implemented within the `core` library.

These are defined using a `@builtin(...)` attribute on the item.

## Traits [↵](#language-items)

Builtin traits provide additional info to the types that implement them, and are required for those types to be used in certain kinds of expressions.

More detailed info about each item can be found in the relavent interface's documentation

trait               | language item           | description
--------------------|-------------------------|----------------------------------------------------------------------------------------------------------
[`Clone`]           | `clone`                 | Supertrait of `Copy` and used in the generation of record types
[`Copy`]            | `copy`                  | The type may be copied using a byte-wise memcpy and used in the generation of record types
[`Eq`]              | `equality`              | see [comparison operators]
[`Iden`]            | `identity`              | see [comparison operators]
[`Ord`]             | `ord`                   | see [comparison operators]
[`TotalOrd`]        | `total_ord`             | see [comparison operators]
[`StructEq`]        | `structural_equality`   | The type may be used as a constant in [pattern matching], utilizing `Eq`
[`StructIden`]      | `structural_identity`   | The type may be used as a constant in [pattern matching], utilizing `Iden`
[`OptAccess`]       | `optional_access`       | The type may be used with [optional chaining]
[`DispatchFromDyn`] | `dispatch_from_dynamic` | The type may be used to wrap a [receiver]
[`From`]            | `from`                  | The type may be used as the result of [`as`]
[`TryFrom`]         | `try_from`              | The type may be used as the result of [`as?`]
[`UnwrapFrom`]      | `unwrap_from`           | The type may be used as the result of [`as!`]
[`Default`]         | `default`               | Allows a type to define a default (runtime created) value and indicates that it may be used with [default initialization].
[`Index`]           | `index`                 | The type may be used in an [index expression]
[`IndexMut`]        | `index_mut`             | The type may be used in a mutable [index expression]
[`TryIndex`]        | `try_index`             | The type may be used in a try [index expression]
[`TryIndexMut`]     | `try_index_mut`         | The type may be used in a mutable try [index expression]
[`Fn`]              | `fn`                    | The type may be called like a function by immutable reference, see [function traits]
[`FnMut`]           | `fn_mut`                | The type may be called like a function by mutable reference, see [function traits]
[`FnOnce`]          | `fn_once`               | The type may be called like a function by move, see [function traits]
[`IntoIterator`]    | `into_iterator`         | Can produce an iterator and allows it to be used in a [`for` loop]
[`Iterator`]        | `iterator`              | The type may be used as an iterator
[`Try`]             | `try`                   | The type may be used in a [`try` expression]
[`TryUnwrap`]       | `try_unwrap`            | The type may be used in a [`try!` expression]
[`Catch`]           | `catch`                 | The type may be used in a [`catch` expression]
[`Hash`]            | `hash`                  | Allows the type to be hashed and is used in the generation of record types
[`Sized`]           | `sized`                 | Indicates a type is sized
[`Unsize`]          | `unsize`                | The type may be coerced to an unsized type
[`CoerceUnsized`]   | `coerce_unsized`        | The type can contain an `Unsize` type and be coerced to a type contains the usized version, i.e. `T[U is Unsize[V]]` -> `T[V]`
[`Drop`]            | `drop`                  | Defines how a type should be dropped at the end of the scope
[`Borrow`]          | `borrow`                | The type may be borrowed as a reference to another type
[`BorrowMut`]       | `borrow_mut`            | The type may be borrowed as a mutable reference to another type
[`Deref`]           | `deref`                 | The type may be dereferenced
[`DerefMut`]        | `deref_mut`             | The type may be dereferenced mutable
[`DerefMove`]       | `deref_move`            | The type may be dereferenced and move a value out

## Types [↵](#language-items)

Builtin type provides types which the compiler handles differently compared to all other types.

type                    | language item                | description
------------------------|------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------
[`Option`]              | `option`                     | Underlying type for [optional types]
[`Result`]              | `result`                     | Underlying type for [result types]
[`KeyValue`]            | `key_value`                  | Type produces by a [key-value expression]
[`KeyPath`]             | `key_path`                   | Type produces by a [key-path expression]
[`ManuallyDrop`]        | `manually_drop`              | The contained type will not be automatically dropped by the compiler
[`ConditionalDrop`]     | `conditional_drop`           | The contained type will only be dropped when a condition is fulfilled, used by the [`@drop_flag`] attribute
[`MaybeUninit`]         | `maybe_uninit`               | The contained type may be in an uninitialized state, but can be used in such a state, without invoking [IB]. It is not automatically dropped
[`DecLiteral`]          | `decimal_literal`            | Type produced by a [decimal literal]
[`DecFloatLiteral`]     | `decimal_float_literal`      | Type produced by a [decimal float literal]
[`BinLiteral`]          | `binary_literal`             | Type produced by a [binary literal]
[`OctLiteral`]          | `octal_literal`              | Type produced by a [octal literal]
[`HexLiteral`]          | `hexadecimal_literal`        | Type produced by a [hexadecimal literal]
[`HexFloatLiteral`]     | `hexadecimal_float_literal`  | Type produced by a [hexadecimal float literal]
[`BoolLiteral`]         | `bool_literal`               | Type produced by a [boolean literal]
[`CharLiteral`]         | `character_literal`          | Type produced by a [character literal]
[`StringLiteral`]       | `string_literal`             | Type produced by a [string literal]
[`InterpStringLiteral`] | `intepolated_string_literal` | Type produced by a [interpolated string literal]

## Precedences [↵](#language-items)

There are 2 builtin precedences: `highest_precedence` and `lowest_precedence`.
As their names imply, they represent the highest and lowest precedence an operator can have, respectively.

More info can be found in the [precedences section].


[`@drop_flag`]:                ./attributes/code-generation.md#drop_flag-
[pattern matching]:            ./expressions/match-expressions.md
[optional chaining]:           ./expressions/field-access-expressions.md#optional-chaining-
[index expression]:            ./expressions/index-expressions.md
[`as`]:                        ./expressions/type-cast-expressions.md
[`as?`]:                       ./expressions/type-cast-expressions.md
[`as!`]:                       ./expressions/type-cast-expressions.md
[`catch` expression]:          ./expressions/catch-expressions.md
[key-value expression]:        ./expressions/key-value-expressions.md
[key-path expression]:         ./expressions/key-path-expressions.md
[`for` loop]:                  ./expressions/loop-expressions/for-expressions.md
[`try` expression]:            ./expressions/try-expressions.md
[`try!` expression]:           ./expressions/try-expressions.md
[IB]:                          ./illegal-behavior.md
[receiver]:                    ./items/functions.md#methods-
[default initialization]:      ./items/initializers.md#default-initialization-
[decimal literal]:             ./literals.md#decimal-literal-
[decimal float literal]:       ./literals.md#floating-point-literals-
[binary literal]:              ./literals.md#binary-literals-
[octal literal]:               ./literals.md#octal-literals-
[hexadecimal literal]:         ./literals.md#hexadecimal-floating-point-literals-
[hexadecimal float literal]:   ./literals.md#hexadecimal-integer-literals-
[boolean literal]:             ./literals.md#boolean-literals-
[character literal]:           ./literals.md#character-literals-
[string literal]:              ./literals.md#string-literals-
[interpolated string literal]: ./literals.md#string-interpolation-
[comparison operators]:        ./operators/special-operators.md#comparison-
[precedences section]:         ./precedences.md
[function traits]:             ./type-system/types/function-like-types/closure-types.md#call-traits-and-coercions-
[optional types]:              ./type-system/types/abstract-types/optional-types.md
[result types]:                ./type-system/types/abstract-types/result-types.md

[`Clone`]:                     #traits- "Todo: link to docs"
[`Copy`]:                      #traits- "Todo: link to docs"
[`Eq`]:                        #traits- "Todo: link to docs"
[`Iden`]:                      #traits- "Todo: link to docs"
[`Ord`]:                       #traits- "Todo: link to docs"
[`TotalOrd`]:                  #traits- "Todo: link to docs"
[`StructEq`]:                  #traits- "Todo: link to docs"
[`StructIden`]:                #traits- "Todo: link to docs"
[`OptAccess`]:                 #traits- "Todo: link to docs"
[`DispatchFromDyn`]:           #traits- "Todo: link to docs"
[`From`]:                      #traits- "Todo: link to docs"
[`TryFrom`]:                   #traits- "Todo: link to docs"
[`UnwrapFrom`]:                #traits- "Todo: link to docs"
[`Default`]:                   #traits- "Todo: link to docs"
[`Index`]:                     #traits- "Todo: link to docs"
[`IndexMut`]:                  #traits- "Todo: link to docs"
[`TryIndex`]:                  #traits- "Todo: link to docs"
[`TryIndexMut`]:               #traits- "Todo: link to docs"
[`Fn`]:                        #traits- "Todo: link to docs"
[`FnMut`]:                     #traits- "Todo: link to docs"
[`FnOnce`]:                    #traits- "Todo: link to docs"
[`IntoIterator`]:              #traits- "Todo: link to docs"
[`Iterator`]:                  #traits- "Todo: link to docs"
[`Try`]:                       #traits- "Todo: link to docs"
[`TryUnwrap`]:                 #traits- "Todo: link to docs"
[`Catch`]:                     #traits- "Todo: link to docs"
[`Hash`]:                      #traits- "Todo: link to docs"
[`Sized`]:                     #traits- "Todo: link to docs"
[`Unsize`]:                    #traits- "Todo: link to docs"
[`CoerceUnsized`]:             #traits- "Todo: link to docs"
[`Drop`]:                      #traits- "Todo: link to docs"
[`Borrow`]:                    #traits- "Todo: link to docs"
[`BorrowMut`]:                 #traits- "Todo: link to docs"
[`Deref`]:                     #traits- "Todo: link to docs"
[`DerefMut`]:                  #traits- "Todo: link to docs"
[`DerefMove`]:                 #traits- "Todo: link to docs"
[`Option`]:                    #types- "Todo: link to docs"
[`Result`]:                    #types- "Todo: link to docs"
[`KeyValue`]:                  #types- "Todo: link to docs"
[`KeyPath`]:                   #types- "Todo: link to docs"
[`ManuallyDrop`]:              #types- "Todo: link to docs"
[`ConditionalDrop`]:           #types- "Todo: link to docs"
[`MaybeUninit`]:               #types- "Todo: link to docs"
[`DecLiteral`]:                #types- "Todo: link to docs"
[`DecFloatLiteral`]:           #types- "Todo: link to docs"
[`BinLiteral`]:                #types- "Todo: link to docs"
[`OctLiteral`]:                #types- "Todo: link to docs"
[`HexLiteral`]:                #types- "Todo: link to docs"
[`HexFloatLiteral`]:           #types- "Todo: link to docs"
[`BoolLiteral`]:               #types- "Todo: link to docs"
[`CharLiteral`]:               #types- "Todo: link to docs"
[`StringLiteral`]:             #types- "Todo: link to docs"
[`InterpStringLiteral`]:       #types- "Todo: link to docs"


# Old

## Traits [↵](#language-builtins-)

###  `ResultBuilder` [↵](#traits-)

The corresponding language item is `result_builder`.

Result builder allow types to be constructed in a much more ergonomic way.
It allows a closure with a comma seperated end-expression to be converted into code using the result builder.

The closures using a result builder are annotated using an attribute with the result builder's type as their name.