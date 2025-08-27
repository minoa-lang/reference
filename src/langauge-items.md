# Language items

A language builtins, also known as language items, are special items that the compiler understands as having a special meaning.
All these types are implemented in the `core` library.

These are define either by `@builtin(...)` attribute or as builtin metafunctions.

Each langauge builtin has a corresponding builtin name.

## Traits [↵](#language-builtins-)

### `Clone` [↵](#traits-)

The corresponding builtin name is: `clone`

`Clone` is a supertrait for `Copy`

`Clone` is also used in the generation of record.

### `Copy` [↵](#traits-)

The corresponding builtin name is `copy`
This has `Clone` as a supertrait.

`Copy` indicates to the compiler that the type may be copies using a byte-wise memcpy.

`Copy` is only allowed to be implemented when the type:
- implements its supertrait [`Clone`](#clone-)
- does not manually implement [`Clone`](#clone-)
- All its fields/elements implement `Copy`

`Copy` is also used in the generation of record types.

### `Equality` [↵](#traits-)

The corrsponding builtin name is `equality`.

For more info, see [comparison operators].

### `Identity` [↵](#traits-)

The corresponding builtin name is `identity`.

For more info, see [comparison operators].

### `Ord` [↵](#traits-)

The corresponding builtin name is `ord`.

For more info, see [comparison operators].

###  `TotalOrd` [↵](#traits-)

The corresponding builtin name is `total_ord`.

For more info, see [comparison operators].

###  `StructuralEquality` [↵](#traits-)

The corresponding builtin name is `structural_equality`.
This has `Equality` as a super trait.

This is an `unsafe` trait.

It is used to indicate to pattern matching that a constant of the implementing type can be used as a constant in pattern matching.
When a constant of this type is encountered, it will use `Equality::eq` to compare if the values are the same.

`StructuralEquality` indicates a struct is compared as follows:
- All fields are individually checked

This trait is mutually exclusive with `StructuralIdentity`.

It requires that `Equality` is implemented as `const`, i.e. it can run its functions at compile-time.

###  `StructuralIdentity` [↵](#traits-)

The corresponding builtin name is `structural_identity`
This has `Identity` as a super trait.

This is an `unsafe` trait.

It is used to indicate to pattern matching that a constant of the implementing type can be used as a constant in pattern matching.
When a constant of this type is encountered, it will use `Identity::identity` to compare if the values are the same.

This trait is mutually exclusive with `StructuralEquality`.

It requires that `Identity` is implemented as `const`, i.e. it can run its functions at compile-time.

###  `OptAccess` [↵](#traits-)

The corresponding builtin name is `opt_access`.

This is used to indicate that any null-propagating expression may be performed on the type.

###  `DispatchFromDyn` [↵](#traits-)

The corresponding builin name is `dispatch_from_dyn`.

This trait indicates that the type may be used to wrap a reciever.

This differs between implementing a `Deref`-style interface, as it only allows it to be called when the specific type `U` wraps the implementing type `T`.
This requires that `U` implements `DispatchFromDyn(T)`.

###  `From` [↵](#traits-)

The corresponding builtin name is `from`.

This trait corresponds to a call to `as`.

It works in tandem with the `To` trait, only one of them may be manually implemented.
Implementing either trait will implicitly implement the other.

###  `TryFrom` [↵](#traits-)

The corresponding builtin name is `try_from`.

This trait corresponds to a call to `as?`.

It works in tandem with the `TryTo` trait, only one of them may be manually implemented.
Implementing either trait will implicitly implement the other.

###  `UnwrapFrom` [↵](#traits-)

The corresponding builtin name is `try_err_from`.

This trait corresponds to a call to `as!`.

It works in tandem with the `UnwrapTo` trait, only one of them may be manually implemented.
Implementing either trait will implicitly implement the other.


###  `Default` [↵](#traits-)

The corresponding builtin name is `default`.

This trait indicates a default value for a type.
Unlike default fields, it can use runtime code.

###  `Index` [↵](#traits-)

The corresponding builtin name is `index`.

This trait defines the index expression operator, i.e. `a[b]`, taking in `&self`, and returning `T`..

###  `IndexMut` [↵](#traits-)

The corresponding builtin name is `index_mut`.

This trait defines the mutable index expression operator, i.e. `a[b]`, taking in `&mut self`, and returning `T`..

###  `OptIndex` [↵](#traits-)

The corresponding builtin name is `opt_index`.

This trait defines the optional index expression operator, i.e. `a[?b]`, taking in `&self`, and returning `?T`..

###  `OptIndexMut` [↵](#traits-)

The corresponding builtin name is `opt_index_mut`.

This trait defines the mutable index expression operator, i.e. `a[?b]`, taking in `&mut self`, and returning `?T`.

###  `RevIndex` [↵](#traits-)

The corresponding builtin name is `rev_index`.

This trait defines the index expression operator, i.e. `a[^b]`, taking in `&self`, and returning `T`..

###  `RevIndexMut` [↵](#traits-)

The corresponding builtin name is `rev_index_mut`.

This trait defines the mutable index expression operator, i.e. `a[^b]`, taking in `&mut self`, and returning `T`..

###  `RevOptIndex` [↵](#traits-)

The corresponding builtin name is `rev_opt_index`.

This trait defines the optional index expression operator, i.e. `a[?^b]`, taking in `&self`, and returning `?T`..

###  `RevOptIndexMut` [↵](#traits-)

The corresponding builtin name is `rev_opt_index_mut`.

This trait defines the mutable index expression operator, i.e. `a[?^b]`, taking in `&mut self`, and returning `?T`.

###  `Fn` [↵](#traits-)

The corresponding builtin name is `fn`.

This trait indicates that a type may be called as a function.
The type is accessed as `&self`, and may thus not modify its own state.

###  `FnMut` [↵](#traits-)

The corresponding builtin name is `fn_mut`.

This trait indicates that a type may be called as a function.
The type is accessed as `&mut self`, and may thus modify its own state.

###  `FnOnce` [↵](#traits-)

The corresponding builtin name is `fn_once`.

This trait indicates that a type may be called as a function.
The type is accessed as `self`, and is therefore moved.

###  `IntoIterator` [↵](#traits-)

The corresponding builtin name is `into_iterator`.

This trait indicates that a type which can be converted into an iterator to be able to be used in a [`for` expression].

###  `Iterator` [↵](#traits-)

The corresponding builtin name is `into_iterator`.

This trait indicates that a type is iterable and can be used inside of a 

###  `Try` [↵](#traits-)

The corresponding builtin name is `try`.

This trait allows for a type to have a [`try` expression].
This also defines the postfix `?` (try) operator.

###  `TryUnwrap` [↵](#traits-)

The corresponding builtin name is `try_unwrap`.

This trait allows for a type to have a [`try!` expression] be called on it.
This also defines the postfix `!` (unwrap) operator.

###  `Catch` [↵](#traits-)

The corresponding builtin name is `catch`.

This trait is used to check for an erronous value when calling a catch expression on it.
It is also defines the `??` operator.

###  `Hash` [↵](#traits-)

The corresponding builtin name is `hash`.

This builtin only exists to allow the compiler to automatically generate a hashing function for structural types.

###  `Sized` [↵](#traits-)

The corresponding builtin name is `sized`.

This trait marks all types that have a known size.

###  `Unsize` [↵](#traits-)

The corresponding builtin name is `unsize`.

This trait indicates if a type may be coerced to which unsized types.
It is used in tandem with [`CoerceUnsized`].

###  `CoerceUnsized` [↵](#traits-)

The corresponding builtin name is `coerce_unsized`.

This trait allows types that contain an `Unsize` type to be coerced into a type containing its unsized version, i.e. `T(U is Unsize(V))` -> `T(V)`

###  `Drop` [↵](#traits-)

The corresponding builtin name is `drop`.

This trait defines how a type should be dropped when going out of scope.

###  `Borrow` [↵](#traits-)

The corresponding builitn name is `borrow`

This trait defines the `&` operator, but also indicates to the compiler that the value has had a shared borrow taken.

###  `BorrowMut` [↵](#traits-)

The corresponding builitn name is `borrow_mut`

This trait defines the `&mut` operator, but also indicates to the compiler that the value has had a shared borrow taken.

###  `Deref` [↵](#types-)

The corresponding builtin name is `deref`.

This trait implements the dereference operator.
It is also used by the compiler to keep track of borrowing.

###  `DerefMut` [↵](#types-)

The corresponding builtin name is `deref_mut`.

This trait implements the dereference operator.
It is also used by the compiler to keep track of borrowing.

###  `DerefMove` [↵](#types-)

The corresponding builtin name is `deref_move`.

This trait implements the dereference operator.
It is also used by the compiler to keep track of borrowing.

###  `LogicalAnd` [↵](#traits-)

The correspodning builtin name is `logical_and`

This trait implements the `&&` opeator.
It also is used to manage let-binding chains.

###  `LogicalOr` [↵](#traits-)

The correspodning builtin name is `logical_and`

This trait implements the `&&` opeator.
It also is used to manage let-binding chains.

###  `ResultBuilder` [↵](#traits-)

The corresponding language item is `result_builder`.

Result builder allow types to be constructed in a much more ergonomic way.
It allows a closure with a comma seperated end-expression to be converted into code using the result builder.

The closures using a result builder are annotated using an attribute with the result builder's type as their name.

## 24.2. Types [↵](#language-builtins-)

###  `Option` [↵](#types-)

The corresponding builtin name is `option`.

This represents the desugared [nullable type], which wraps another type and allows it to be `.None`

###  `Result` [↵](#types-)

The corresponding builtin name is `result`.

This represents the desugared [result type], allowing a value to be valid or have an error value

###  `KeyValue` [↵](#types-)

The corresponding builtin name is `key_value`

This type stored key-value pairs generated by key-value array list expressions or array comprehensions.

###  `KeyPath` [↵](#types-)

The corresponding builtin name is `key_path`.

This types stores the value created by a [key-path expression].

###  `ManuallyDrop` [↵](#types-)

The corresponding builtin name is `manually_drop`.

This type can wrap another type and prevent its `Drop.drop()` function from being called when it goes out of scope.

###  `MaybeUninit` [↵](#types-)

The corresponding builtin name is `maybe_uninit`.

This types wraps any type and allows it to be in an uninitialized state.
This is not illegal behavior, as the compiler knows that the underlying data is in an invalid state.

To be able to use the value wrapped by `MaybeUninit`, it needs to explicitly be declared as fully initialized.

Like `ManuallyDrop`, any value wrapped within this type is **not** automatically dropped.

> _Todo_: There might be compiler checks in the future that ensures all member are initialized.

###  `InterplatedString` [↵](#types-)

The corresponding builtin name is `interpolated_string`.

This type is used to represent any string which contains interpolations, where the expressions provided will be treated as lazy values.

###  `meta.TokenStream` [↵](#types-)

The corresponding builtin name is `meta_token_stream`.

This type represents a compiler provided token stream used by meta-functions to receive and return arbitrary code.

###  `meta.FragmentStream` [↵](#types-)

The corresponding builtin name is `meta_fragment_stream`.

This type represents the input for pattern parse and pattern match functions.

###  `meta.Ast` [↵](#types-)

The corresponding builtin name is `meta_ast`.

This type represents an AST sub-tree used by meta-functions to receive and return arbitrary code.

###  `meta.AttribData` [↵](#types-)

The corresponding builtin name is `meta_attrib_data`.

## 24.3. Precedences [↵](#language-builtins-)

The langauge knows about 2 precedences: `lowest_precedence` and `highest_precedence`.
These define both the highest and lowest precedence and are used to order any user defined precedences.

More info can be found [here](./precedences.md).

## 24.4. Meta-functions [↵](#language-builtins-)

Meta builtins, unlike other builtins are not located within the `core` library, but in the `meta` library.
The represent special meta-functions which need additional compiler support to fully work.

> _Note_: Some of these may move to fully being standalone meta-functions once the required infrastrucure needed has been finalized, like the self-hosted compiler API.
>         This is indicated for each function

###  `line` [↵](#meta-functions-)

The corresponding builtin name is `line`.

This meta-variable returns the line on which the meta-attribute is invoked.

###  `column` [↵](#meta-functions-)

The corresponding builtin name is `column`.

This meta-variable returns the column on which the meta-attribute is invoked.

###  `group` [↵](#meta-functions-)

The corresponding builtin name is `group`.

The meta-variable returns the group name that the current package is in, or `None` if there in no group name.

###  `package` [↵](#meta-functions-)

The corresponding builtin name is `package`.

The meta-variable returns the current package name.

###  `library` [↵](#meta-functions-)

The corresponding builtin name is `library`.

The meta-variable returns the current library name.

###  `file` [↵](#meta-functions-)

The corersponding builtin name is `file`

The meta-function gets the file name.

> _Todo_: Add in docs: It can return the name, relative path to ..., and full path

###  `context` [↵](#meta-functions-)

The corresponding builtin name is `context`.

The meta-variable gives access to the implicit context.

###  `embed` [↵](#meta-functions-)

The corresponding builtin name is `embed`.

The meta-function allows external data to be directly included within code.

This is the only meta-function that has access to the file system, but solely relative to the project's root.

###  `asm` [↵](#meta-functions-)

The corresponding builtin name is `asm`

This meta-function allows for inline assembly directly within code

See its documentation for more info.

###  `tokenize` [↵](#meta-functions-)

The corresponding builtin name is `meta_tokenize`.

This meta-function is used to generate tokens from a given input.
It is also used to let the compiler know which tokenize meta-function to use for [meta-blocks].

See [`#tokenize`].

> _Note_: In the future, the sole purpose of this builtin is to indicate the tokenize function for meta-blocks and will have an independent implementation

###  `partiah_synth` [↵](#meta-functions-)

The corresponding builtin name is `partiah_synth`.

This meta-function is used to generate a fragment stream from a given input.
It is also used to let the compiler know which synthesize meta-function to use for [meta-blocks].
 
See [`#synthesize`.

###  `synthesize` [↵](#meta-functions-)

The corresponding builtin name is `meta_synthesize`.

This meta-function is used to generate an ast from a given input.
It is also used to let the compiler know which synthesize meta-function to use for [meta-blocks].
 
See [`#synthesize`].

> _Note_: In the future, the sole purpose of this builtin is to indicate the tokenize function for meta-blocks and will have an independent implementation

###  `pattern_parse` [↵](#meta-functions-)

The corresponding builtin name is `meta_pattern_parse`.

This meta-function is used to parse a given token input into a partial AST.

See [`#pattern_parse`](#1382-pattern_parse-)

> _Note_: In the future, this will be a standalone meta-function

###  `pattern_match` [↵](#meta-functions-)

The corresponding builtin name is `meta_pattern_match`.

This meta-function is used to match an input to one of the provided patterns or a wildcard, and execute that branch.

See [`#pattern_match`](#1383-pattern_match-)

> _Note_: In the future, this will be a standalone meta-function

###  `invoke_repr` [↵](#meta-functions-)

The corresponding builtin name is `invoke_repr`.

The meta-function allows the program to get the representation of a value at its invocation site.

See [`#invoke_rep`](#1384-invoke_repr-)

###  `meta.library` [↵](#meta-functions-)

The corresponding builtin name is `meta_library`.

Not to be confused with the core [`library`](./package-structure.md) meta-function.
This meta-variable is used to generate paths within a meta-attribute relative to the library the meta-function is defined in.

See [`#library`](./metaprogramming/meta-utilities.md#library--package-)

###  `meta.package` [↵](#meta-functions-)

The corresponding builtin name is `meta_package`.

Not to be confused with the core [`package`](./package-structure.md) meta-function.
This meta-variable is used to generate paths within a meta-attribute relative to the library the meta-function is defined in.

See [`#library`](./metaprogramming/meta-utilities.md#library--package-)



[`CoerceUnsized`]:      #coerceunsized-
[key-path expression]:  ./expressions/key-path-expressions.md
[`for` expression]:     ./expressions/loop-expressions.md#for-expression-
[`try` expression]:     ./expressions/try-expressions.md
[`try!` expression]:    ./expressions/try-expressions.md
[`#library`]:           ./metaprogramming/meta-utilities.md#library--package-
[`#package`]:           ./metaprogramming/meta-utilities.md#library--package-
[meta-blocks]:          ./metaprogramming/meta-utilities.md#meta-blocks-
[`#invoked_rep`]:       ./metaprogramming/meta-utilities.md#invoked_repr-
[`#pattern_match`]:     ./metaprogramming/meta-utilities.md#pattern_match-
[`#pattern_parse`]:     ./metaprogramming/meta-utilities.md#pattern_parse-
[`#tokenize`]:          ./metaprogramming/meta-utilities.md#tokenize-partial_synth--synthesize-
[`#partiah_synth`]:     ./metaprogramming/meta-utilities.md#tokenize-partial_synth--synthesize-
[`#synthesize`]:        ./metaprogramming/meta-utilities.md#tokenize-partial_synth--synthesize-
[comparison operators]: ./operators/core-operators.md#comparison-operators-
[nullable type]:        ./type-system/types/opaque-types.md
[result type]:          ./type-system/types/result-types.md