# Keywords

Keywords represent names within the code that have a special meaning in the language, such as declaring a function.

There are 4 types of keywords:
- strong
- reserved
- weak
- pattern

## Strong keywords [↵](#keywords-)

A strong keyword is a keyword that always has a meaning, regardless of where in the code it is located, and can therefore not be used for anything else.

A list of strong keywords can be found below (in alphabetic order):
- `as`
- `as?`
- `as!`
- `assert`
- `async`
- `await`
- `bool`
- `break`
- `catch`
- `char`
- `char7`
- `char8`
- `char16`
- `char32`
- `const`
- `constraint`
- `continue`
- `cstr`
- `defer`
- `do`
- `dyn`
- `else`
- `enum`
- `errdefer`
- `false`
- `fallthrough`
- `fn`
- `for`
- `if`
- `in`
- `!in`
- `impl`
- `iptr`
- `is`
- `!is`
- `isize`
- `let`
- `let?`
- `let!`
- `loop`
- `null`
- `match`
- `mod`
- `move`
- `mut`
- `pub`
- `ref`
- `return`
- `safe`
- `self`
- `static`
- `str`
- `str7`
- `str8`
- `str16`
- `str32`
- `struct`
- `throw`
- `trait`
- `true`
- `try`
- `try!`
- `type`
- `unsafe`
- `uptr`
- `use`
- `usize`
- `with`
- `while`
- `when`
- `where`
- `yield`

## Reserved keywords [↵](#keywords-)

A reserved keyword is keyword that is not currently used, but has been set aside as not being possible to be used by the langauge for future use.

A list of reserved keywords can be found below (in alphabetic order):
- `override`
- `priv`

## Weak keywords [↵](#keywords-)

A weak keyword is a keyword that is dependent on the surrounding context and can be used anywhere outside.
In addition, each keyword has additional info in which use-case it is counted as an actual keyword.

A list of weak keywords can be found below (in alphabetic order):
- `accessor`: to declare a [meta attribute role]
- `adapt`: to declare an [adapt type alias]
- `alias`: to declare an [disambiguation alias]
- `align`: to declare the alignment of a [pointer type]
- `allowzero`: to declare an [`allowzero` pointer type]
- `arbitrary`: as part of meta [hygiene info]
- `assign`: as part of an [operator] declaration
- `associativity`: as part of a [precedence] declaration
- `attr`: to declare a [meta attribute] or [meta attribute role] or as part of a [meta pattern]
- `bench`: to declare a [benchmark]
- `bitfield`: to declare a [bitfield]
- `block`: as part of a [meta pattern]
- `chain`: as part of an [operator] declaration
- `consume`: as part of an [operator] declaration
- `derive`: to declare a [meta attribute role]
- `did_set`: to declare a [property observer]
- `distinct`: to declare a [distinct type alias]
- `expr`: as part of a [meta pattern]
- `extend`: as part of a [trait implementation]
- `flag`: to declare a [flag enum]
- `field`: within a [field property]
- `field_convert`: as part of meta [hygiene info]
- `full`: to declare a [meta attribute role]
- `get`: to declare a [property getter]
- `higher_than`: as part of a [precedence] declaration
- `iden`: as part of a [meta pattern]
- `infix`: as part of an [operator] declaration
- `init`: to declare an [initializer]
- `invar`: as part of a [function contract]
- `item`: as part of a [meta pattern]
- `lazy`: to declare a [lazy parameter], or as part of an [operator] declaration
- `lib`: as part of a [visibility specifier]
- `lit`: as part of a [meta pattern]
- `literal`: as part of a [literal operator]
- `lower_than`: as part of a [precedence] declaration
- `lsb`: as part of the bit order for a [bitfield]
- `member`: to declare a [meta attribute role]
- `member_attr`: to declare a [meta attribute role]
- `meta`: to declare a [meta function]
- `meta_pat`: as part of a [meta pattern]
- `msb`: as part of the bit order for a [bitfield]
- `named`: as part of meta [hygiene info]
- `names`: as part of meta [hygiene info]
- `opaque`: to declare an [opaque type]
- `overloaded`: as part of meta [hygiene info]
- `package`: as part of a [visibility specifier]
- `pat`: as part of a [meta pattern]
- `path`: as part of a [meta pattern]
- `peer`: to declare a [meta attribute role]
- `post`: as part of a [function contract]
- `postfix`: as part of an [operator] declaration
- `pre`: as part of a [function contract]
- `precedence`: to declare a [precedence]
- `prefix`: as part of an [operator] declaration or meta [hygiene info]
- `prop`: to declare a [property]
- `raw`: to get a [raw pointer value]
- `record`: to declare a [record struct], [record tuple struct], [record enum], or [record bitfield]
- `sealed`: to declare a [sealed trait]
- `set`: to declare a [property setter]
- `sparse`: to declare a [sparse array type]
- `stmt`: as part of a [meta pattern]
- `suffix`: as part of meta [hygiene info]
- `super`: as part of a [visibility specifier], or as a [simple path start]
- `template`: to declare a [template string function]
- `test`: to declare a [test] item
- `tls`: to declare a [thread-local static]
- `toks`: as part of a [meta pattern]
- `tt`: as part of a [meta pattern]
- `ty`: as part of a [meta pattern]
- `union`: to declare a [union]
- `unique`: as part of a [closure capture list]
- `vis`: as part of a [meta pattern]
- `volatile`: to declare a [volatile pointer]
- `will_set`: to declare a [property observer]
- `with`: as part of a [test] item



## Pattern keywords

A pattern keyword is a special keyword, that instead of following a specific value, is parsed using a pattern.
These are all strong keywords.
The pattern is defined by having `{}`, surrouonding a pattern definition, which is specified after the keyword.
It also defines optional characters between `[]`, separated by `|`.

A list of weak keywords can be found below (in alphabetic order):
- `b{N}`: where `N` is any whole integer <= 65536.
- `u{N}[le|be]`: where `N` is any whole integer <= 65536.
- `i{N}[le|be]`: where `N` is any whole integer <= 65536.
- `f(16|32|64|128)[le|be]`



[function contract]:               ../constracts.md#function-contracts-
[closure capture list]:            ../expressions/closure-expressions.md
[simple path start]:               ../identifiers-paths.md#simple-paths-
[disambiguation alias]:            ../items/disambig-aliases.md
[flag enum]:                       ../items/enums.md#flag-enum-
[lazy parameter]:                  ../items/functions.md#lazy-parameters-
[template string function]:        ../items/functions.md#template-string-functions-
[initializer]:                     ../items/initializers.md
[trait implementation]:            ../items/implementations.md#trait-implementation-
[property]:                        ../items/properties.md
[field property]:                  ../items/properties.md#field-property
[property getter]:                 ../items/properties.md#getters-
[property observer]:               ../items/properties.md#observers-
[property setter]:                 ../items/properties.md#setters-
[thread-local static]:             ../items/statics.md#thread-local-storage-
[test]:                            ../items/tests.md
[benchmark]:                       ../items/tests.md#benchmarks-
[sealed trait]:                    ../items/traits.md
[adapt type alias]:                ../items/type-aliases.md#adapt-type-aliases-
[distinct type alias]:             ../items/type-aliases.md#distinct-type-aliases-
[meta function]:                   ../metaprogramming.md
[meta attribute]:                  ../metaprogramming.md#meta-attributes-
[meta attribute role]:             ../metaprogramming.md#attribute-roles-
[meta pattern]:                    ../metaprogramming.md#meta-patterns-
[hygiene info]:                    ../metaprogramming.md#hygiene-info-at-the-declaration-site-
[operator]:                        ../operators.md#operator-items-
[literal operator]:                ../operators/literal-operators.md
[raw pointer value]:               ../operators/special-operators.md#pointer-borrowing-
[precedence]:                      ../precedences.md#user-defined-precedence-
[sparse array type]:               ../type-system/types/array-types.md
[bitfield]:                        ../type-system/types/bitfield-types.md
[opaque type]:                     ../type-system/types/opaque-types.md
[record bitfield]:                 ../type-system/types/bitfield-types.md#record-bitfield-types-
[record enum]:                     ../type-system/types/enum-types.md#record-enum-types-
[pointer type]:                    ../type-system/types/pointer-types.md#alignment-
[`allowzero` pointer type]:        ../type-system/types/pointer-types.md#allowzero-
[volatile pointer]:                ../type-system/types/pointer-types.md#volatile-pointers-
[record struct]:                   ../type-system/types/struct-types.md#record-structs-
[record tuple struct]:             ../type-system/types/tuple-struct-types.md#record-tuple-structs-
[union]:                           ../type-system/types/union-types.m
[visibility specifier]:            ../visibility.md#specifiers-