# Minoa language design

Version: 0.1


> _Todo_: Update occurances of the language's name and file extension withing examples with the correct one.

## Tables of contents

- [Minoa language design](#minoa-language-design)
   - [Tables of contents](#tables-of-contents)
1. [Introduction](#1-introduction-)
   1. [This document is provisional](#this-document-is-provisional-)
   2. [Notation](#notation-)
2. [Source code representation](#2-source-code-representation-)
   1. [Input format](#21-input-format-)
   2. [Byte order markers](#22-byte-order-markers-)
   3. [Shebang](#23-shebang-)
   4. [Normalization](#24-normalization-)
3. [Lexical structure](#3-lexical-structure-)
   1. [Whitespace](#31-whitespace-)
      1. [New line sequences](#311-new-line-sequences-)
   2. [Keywords](#32-keywords-)
      1. [Strong keywords](#321-strong-keywords-)
      2. [Reserved keywords](#322-reserved-keywords-)
      3. [Weak keywords](#323-weak-keywords-)
   3. [Names](#33-names-)
   4. [Punctuation](#34-punctuation-)
   5. [Delimiters](#35-delimiters-)
   6. [Comments](#36-comments-)
      1. [Regular comment](#361-regular-comment-)
      2. [Doc comments](#362-doc-comments-)
         1. [`doc` attribute](#doc-attribute-)
         2. [Doc comment format](#doc-comment-format-)
            1. [Short description](#short-description-)
            2. [Long description](#long-description-)
            3. [Including external text](#including-external-text-)
            4. [Item links](#item-links-)
            5. [Receiver](#receiver-)
            6. [Inferred parameter](#inferred-parameter-)
            7. [Parameter](#parameter-)
            8. [Return](#return-)
            9. [Named return](#named-return-)
            10. [Since](#since-)
            11. [Pre-condition contract](#pre-condition-contract-)
            12. [Post-condition contract](#post-condition-contract-)
            13. [Invariant contract](#invariant-contract-)
            14. [Complexity](#complexity-)
            15. [Important](#important-)
            16. [Warning](#warning-)
            17. [Attention](#attention-)
            18. [Errors](#errors-)
            19. [Issue](#issue-)
            20. [General notes \& exhortations](#general-notes--exhortations-)
            21. [Favicon](#favicon-)
            22. [Logo](#logo-)
            23. [Playground](#playground-)
            24. [Issue tracker base](#issue-tracker-base-)
            25. [No source](#no-source-)
            26. [`inline` and `no_inline`](#inline-and-no_inline)
            27. [`hidden`](#hidden-)
            28. [`alias`](#alias-)
      3. [Examples](#363-examples-)
         1. [Library description](#library-description-)
         2. [Documenting a module](#documenting-a-module-)
4. [Package structure](#4-package-structure-)
   1. [Packages](#41-packages-)
      1. [Groups](#411-groups-)
   2. [Artifacts](#42-artifacts-)
      1. [inaries](#421-binaries-)
      2. [Static ibraries](#422-static-ibraries-)
      3. [Dynamic ibraries](#423-dynamic-ibraries-)
   3. [Modules](#43-modules-)
      1. [Main module](#431-main-module-)
      2. [Module roots](#432-module-roots-)
5. [Names and paths](#5-names-and-paths-)
   1. [Identifiers](#51-identifiers-)
      1. [Trait disambiguation](#511-trait-disambiguation-)
   2. [Paths](#52-paths-)
      1. [Simple paths](#521-simple-paths-)
      2. [Regular paths](#522-regular-paths-)
         1. [Path start](#path-start-)
      3. [Trait paths](#523-trait-paths-)
6. [Literals](#6-literals-)
   1. [Numeric literals](#61-numeric-literals-)
      1. [Decimal literal](#611-decimal-literal-)
         1. [Examples](#examples-)
      2. [Binary literals](#612-binary-literals-)
         1. [Examples](#examples--1)
      3. [Octal literals](#613-octal-literals-)
         1. [Examples](#examples--2)
      4. [Hexadecimal integer literals](#614-hexadecimal-integer-literals-)
         1. [Examples](#examples--3)
      5. [Hexadecimal floating point literals](#615-hexadecimal-floating-point-literals-)
         1. [Examples](#examples--4)
   2. [Boolean literals](#62-boolean-literals-)
   3. [Character literals](#63-character-literals-)
      1. [Escaped characters](#631-escaped-characters-)
         1. [Examples](#examples--5)
   4. [String literals](#64-string-literals-)
      1. [Multi-line string literals](#641-multi-line-string-literals)
         1. [Examples](#examples--6)
      2. [Raw string literals](#642-raw-string-literals)
         1. [Examples](#examples--7)
      3. [String interpolation](#643-string-interpolation)
7. [Items](#7-items-)
   1. [Module item](#71-module-item-)
      1. [Inline modules](#711-inline-modules-)
      2. [File modules](#712-file-modules-)
      3. [Path attribute](#713-path-attribute-)
   2. [Use declarations](#72-use-declarations-)
      1. [Use visibility](#721-use-visibility-)
      2. [Use root](#722-use-root-)
      3. [Use path](#723-use-path-)
      4. [Glob imports](#724-glob-imports-)
      5. [Use groupings](#725-use-groupings-)
         1. [`self` imports](#self-imports-)
      6. [Use aliases](#726-use-aliases-)
         1. [Underscore imports](#underscore-imports-)
      7. [Import ambiguity](#726-import-ambiguity-)
   3. [Function](#73-function-)
      1. [Parameters](#731-parameters-)
         1. [Labels](#labels-)
         2. [Optional parameters](#optional-parameters-)
         3. [Variadic parameters](#variadic-parameters-)
         4. [Deduced parameters](#deduced-parameters-)
         5. [Lazy parameters](#lazy-parameters)
      2. [Return](#732-return-)
      3. [Function body](#733-function-body-)
      4. [Const functions](#734-const-functions-)
      5. [Methods](#735-methods-)
      6. [Trait functions \& methods](#736-trait-functions--methods-)
      7. [External \& exported functions](#737-external--exported-functions-)
      8. [Generator functions](#738-generator-functions)
      9. [Async functions](#739-async-functions-)
         1. [Async generator functions](#async-generator-functions-)
         2. [Async unsafe functions](#async-unsafe-functions-)
      10. [Label-based overloading](#7310-label-based-overloading-)
         1. [Examples](#examples--8)
      11. [String-template functions](#7311-string-template-functions-)
   4. [Type aliases](#74-type-aliases-)
      1. [Distinct type aliases](#741-distinct-type-aliases-)
      2. [Adapt type aliases](#742-adapt-type-aliases-)
      3. [Trait type aliases](#743-trait-type-aliases-)
   5. [Structs](#75-structs-)
      1. [Regular structs](#751-regular-structs-)
      2. [Tuple structure](#752-tuple-structure-)
      3. [Unit structs](#753-unit-structs-)
   6. [Union](#76-union-)
   7. [Enum](#77-enum-)
      1. [Flag enum](#771-flag-enum-)
   8. [Bitfield](#78-bitfield-)
   9. [Const item](#79-const-item-)
       1. [trait constant](#791-trait-constant-)
   10. [Static item](#710-static-item-)
       1. [Thread local storage](#7101-thread-local-storage-)
       2. [Statics and generics](#7103-statics-and-generics-)
       3. [External statics](#7103-external-statics-)
   11. [Properties](#711-properties-)
       1. [Getters](#7111-getters-)
       2. [Setters](#7112-setters-)
          1. [Setter shorthand](#setter-shorthand-)
       3. [Observers](#7113-observers-)
          1. [Observer shorthand](#observer-shorthand-)
       4. [Direct-bind properties](#7114-direct-bind-properties-)
       5. [Get-only properties](#7115-get-only-properties)
       6. [Trait properties](#7116-trait-properties-)
   12. [Trait](#712-trait-)
       1. [Object safety](#7121-object-safety-)
       2. [Supertraits](#7122-supertraits-)
       3. [Unsafe traits](#7123-unsafe-traits-)
       4. [Visibility](#7124-visibility-)
       5. [Trait Items](#7125-trait-items-)
   13. [Implementation](#713-implementation-)
       1. [Inherent implementation](#7131-inherent-implementation-)
       2. [Trait implementation](#7132-trait-implementation-)
          1. [Coherence](#coherence)
       3. [Impl field items](#7133-impl-field-items-)
       4. [Associated items](#7134-associated-items-)
   14. [External \& export blocks](#714-external--export-blocks-)
   15. [Extensions](#715-extensions-)
       1. [Trait extentions](#7151-trait-extentions)
   16. [Initializers](#716-initializers-)
       1. [Default initialization](#7161-default-initialization)
       2. [Initializer delegation](#7162-initializer-delegation)
       3. [Fallible initializers](#7163-fallible-initializers)
   17. [When items](#717-when-items-)
   18. [Tests](#718-tests-)
8. [Statements](#8-statements-)
   1. [Item statement](#81-item-statement-)
   2. [Variable declaration](#82-variable-declaration-)
   3. [Expression statement](#83-expression-statement-)
   4. [Defer statement](#84-defer-statement-)
      1. [Defer-on-error statement](#841-defer-on-error-statement-)
   5. [Guard statements](#85-guard-statements)
9.  [Expressions](#9-expressions-)
    1. [Expression details](#91-expression-details-)
       1. [Place, value \& assign expressions](#911-place-value--assign-expressions-)
          1. [Place expressions](#place-expressions-)
          2. [Assign expressions](#assign-expressions-)
          3. [Value expressions](#value-expressions-)
       2. [Moved \& copied types](#912-moved--copied-types-)
       3. [Mutability](#913-mutability-)
       4. [Temporaries](#914-temporaries-)
       5. [Implicit borrows](#915-implicit-borrows-)
    2. [Literal expression](#92-literal-expression-)
       1. [Literal type conversion](#921-literal-type-conversion-)
    3. [Path expression](#93-path-expression-)
    4. [Unit expression ](#94-unit-expression--)
    5. [Block expression ](#95-block-expression--)
       1. [Unsafe block](#951-unsafe-block-)
       2. [Const block](#952-const-block-)
       3. [Async blocks](#953-async-blocks-)
          1. [Capture modes](#capture-modes-)
          2. [Async context](#async-context-)
          3. [control-flow operators](#control-flow-operators-)
    6. [Operator expression ](#96-operator-expression--)
    7. [Parenthesized expression ](#97-parenthesized-expression--)
    8. [Type cast expression ](#98-type-cast-expression--)
       1. [Builtin casts](#981-builtin-casts-)
          1. [Numeric cast semantics](#numeric-cast-semantics-)
          2. [Enum cast semantics](#enum-cast-semantics-)
          3. [Primtive to integer cast semantics](#primtive-to-integer-cast-semantics-)
          4. [Integer to character cast semantics](#integer-to-character-cast-semantics-)
          5. [Pointer to address cast semantics](#pointer-to-address-cast-semantics-)
          6. [Pointer to pointer cast semantics](#pointer-to-pointer-cast-semantics-)
          7. [Reference to pointer casts](#reference-to-pointer-casts-)
          8. [Array and slice to pointer casts](#array-and-slice-to-pointer-casts-)
          9. [Function cast semantics](#function-cast-semantics-)
          10. [Type to opaque cast semantics](#type-to-opaque-cast-semantics-)
          11. [Opaque to type cast semantics](#opaque-to-type-cast-semantics-)
       2. [Try and unwrap casts](#982-try-and-unwrap-casts-)
    9. [Type check expression ](#99-type-check-expression--)
    10. [Constructing expression](#910-constructing-expression-)
        1. [Tuple expression](#9101-tuple-expression-)
        2. [Array expression](#9102-array-expression-)
           1. [Array list expression](#array-list-expression-)
           2. [Array count expression](#array-count-expression-)
           3. [Array comprehension](#array-comprehension-)
        3. [Struct expressions](#9103-struct-expressions-)
           1. [Struct (\& union) expression](#struct--union-expression-)
              1. [Functional update syntax](#functional-update-syntax-)
              2. [Struct field shorhand](#struct-field-shorhand-)
              3. [Default fields](#default-fields-)
           2. [Tuple struct expression](#tuple-struct-expression-)
           3. [Unit struct](#unit-struct-)
        4. [Vector expression](#9100-vector-expression)
           1. [Vector list expression](#vector-list-expression)
           2. [Vector count expression](#vector-count-expression)
    11. [Index expression](#911-index-expression-)
    12. [Tuple index expression](#912-tuple-index-expression-)
    13. [Call expression](#913-call-expression-)
        1. [Calling methods as functions](#9131-calling-methods-as-functions-)
        2. [Trailing closure](#9132-trailing-closure-)
    14. [Method call expression](#914-method-call-expression-)
        1. [Method lookup](#9141-method-lookup-)
    15. [Field access](#915-field-access-)
        1. [Automatic dereferencing](#9151-automatic-dereferencing-)
        2. [Borrowing](#9152-borrowing-)
    16. [Closure expressions](#916-closure-expressions-)
        1. [Shorthand arguments](#9161-shorthand-arguments-)
        2. [Closure trait implementations](#9162-closure-trait-implementations-)
        3. [Operator methods](#9163-operator-methods-)
        4. [Autoclosures](#9165-autoclosures-)
        5. [Closure argument expressions](#9164-closure-argument-expressions-)
    17. [Full range expression](#917-full-range-expression-)
    18. [If expression](#918-if-expression-)
        1. [Branch conditions \& `let` bindings](#9181-branch-conditions--let-bindings-)
           1. [Optional shorthand](#optional-shorthand-)
           2. [Let bindings](#let-bindings-)
        2. [Result if expressions](#9182-result-if-expressions-)
    19. [Loops](#919-loops-)
        1. [Loop expression](#9191-loop-expression-)
        2. [While expression](#9192-while-expression-)
           1. [While else](#while-else-)
           2. [Result while](#result-while-)
    20. [Do-while expression](#9193-do-while-expression-)
           1. [Do while else](#do-while-else-)
        1. [For expression](#9194-for-expression-)
           1. [For else](#for-else-)
           2. [Result for](#result-for-)
        2. [Labelled block expressions](#9195-labelled-block-expressions-)
        3. [Loop labels](#9196-loop-labels-)
    21. [Match expression](#920-match-expression-)
        1. [Fallthrough labels](#9201-fallthrough-labels-)
    22. [Break expression](#921-break-expression-)
        1. [Break and loop/match values](#9211-break-and-loopmatch-values-)
    23. [Continue expression](#922-continue-expression-)
    24. [Fallthrough expression](#923-fallthrough-expression-)
    25. [Return expression](#924-return-expression-)
        1. [Return in generator functions](#9241-return-in-generator-functions-)
    26. [Underscore expression](#925-underscore-expression-)
    27. [Throw expression](#962-throw-expression-)
    28. [Try expression](#927-try-expression-)
    29. [Catch expression](#928-catch-expression-)
    30. [Comma expression](#929-comma-expression-)
    31. [When expression](#930-when-expression-)
    32. [Template string expressions](#931-template-string-expressions-)
    33. [Key-path expressions](#932-key-path-expressions-)
    34. [Await expressions](#933-await-expressions-)
    35. [Yield expressions](#934-yield-expressions-)
10. [Patterns](#10-patterns-)
    1. [Literal pattern](#101-literal-pattern-)
    2. [Identifier pattern](#102-identifier-pattern-)
       1. [Binding modes](#1021-binding-modes-)
          1. [Determining the default binding move](#determining-the-default-binding-move-)
    3. [Wildcard pattern](#103-wildcard-pattern-)
    4. [Rest pattern](#104-rest-pattern-)
    5. [Range pattern](#105-range-pattern-)
    6. [Reference pattern](#106-reference-pattern-)
    7. [Struct pattern](#107-struct-pattern-)
    8. [Tuple struct pattern](#108-tuple-struct-pattern-)
    9. [Tuple pattern](#109-tuple-pattern-)
    10. [Grouped pattern](#1010-grouped-pattern-)
    11. [Slice pattern](#1011-slice-pattern-)
    12. [Path pattern](#1012-path-pattern-)
    13. [Alternative pattern](#1013-alternative-pattern-)
        1. [Rules alternative patterns](#10131-rules-alternative-patterns-)
    14. [Type check pattern](#1014-type-check-pattern-)
    15. [Guard pattern](#1015-guard-pattern-)
    16. [Constant patterns](#1016-constant-patterns-)
11. [Type System](#11-type-system-)
    1. [Types](#111-types-)
       1. [`type` type](#1111-type-type-)
       2. [Rescursive types](#1112-rescursive-types-)
       3. [Parenthesized types](#1113-parenthesized-types-)
       4. [Primitive types](#1114-primitive-types-)
          1. [Unsigned types](#unsigned-types)
          2. [Signed types](#signed-types)
          3. [Floating-point types](#floating-point-types)
          4. [Boolean types](#boolean-types)
          5. [Character types](#character-types)
       5. [Unit type](#1115-unit-type-)
       6. [Never type](#1116-never-type-)
       7. [Path types](#1117-path-types-)
       8. [Tuple types](#1118-tuple-types-)
          1. [Named tuples](#named-tuples-)
       9. [Array types](#1119-array-types-)
          1. [Sentinel-terminated arrays](#sentinel-terminated-arrays-)
          2. [Enumerated arrays](#enumerated-arrays-)
          3. [Key-value arrays](#key-value-arrays-)
       10. [Slice types](#11110-slice-types-)
           1. [Sentinel-terminated slices](#sentinel-terminated-slices-)
       11. [String slice types](#11111-string-slice-types-)
       12. [Pointer types](#11112-pointer-types-)
           1. [Single element pointers](#single-element-pointers-)
           2. [Multi element pointers](#multi-element-pointers-)
           3. [Sentinel-terminated pointers](#sentinel-terminated-pointers-)
           4. [Volatile pointers](#volatile-pointers-)
           5. [Alignment](#alignment-)
           6. [`allowzero`](#allowzero)
           7. [Provenence](#provenence-)
       13. [Reference types](#11113-reference-types-)
           1. [Shared reference](#shared-reference-)
           2. [Mutable reference](#mutable-reference-)
           3. [Shared xor mutable](#shared-xor-mutable-)
           4. [11.1.14. Optional types](#11114-optional-types-)
       14. [Result types](#11115-result-types-)
       15. [Opaque types](#11116-opaque-types-)
       16. [Struct types](#11117-struct-types-)
          1. [Default struct fields](#default-struct-fields-)
          2. [Use fields](#use-fields-)
          3. [Fields tags](#fields-tags-)
          4. [Record structs](#record-structs-)
          5. [Unit structs](#unit-structs-)
          6. [Generics in struct types](#generics-in-struct-types-)
       17. [11.1.18 Tuple struct types](#11118-tuple-struct-types-)
          1. [Default tuple struct fields](#default-tuple-struct-fields-)
          2. [Record tuple structs](#record-tuple-structs-)
       18. [Union types](#11119-union-types-)
          1. [Union field access](#union-field-access-)
          2. [Pattern matching on unions](#pattern-matching-on-unions-)
          3. [Reference to union fields](#reference-to-union-fields-)
       19. [Enum types](#11120-enum-types-)
          1. [Discriminant](#discriminant-)
             1. [Assigning discriminant values](#assigning-discriminant-values-)
             2. [Accessing discriminant values](#accessing-discriminant-values-)
          2. [Fieldless enum](#fieldless-enum-)
          3. [Record enum types](#record-enum-types-)
          4. [Flag enum types](#flag-enum-types-)
       20. [Bitfield types](#11121-bitfield-types-)
          1. [Record bitfield types](#record-bitfield-types-)
       21. [Function types](#11122-function-types-)
       22. [Function pointer type](#11123-function-pointer-type-)
       23. [Closure types](#11124-closure-types-)
          1. [Passing closures to functions](#passing-closures-to-functions)
          2. [Capture modes](#capture-modes--1)
             1. [Copy values](#copy-values-)
          3. [Capture precision](#capture-precision-)
             1. [Shared prefix](#shared-prefix-)
             2. [Rightmost shared reference truncation](#rightmost-shared-reference-truncation-)
             3. [Wildcard pattern bindings](#wildcard-pattern-bindings-)
             4. [Capturing references in move contexts](#capturing-references-in-move-contexts-)
             5. [Pointer dereference](#pointer-dereference-)
             6. [Union fields](#union-fields-)
             7. [References to unaligned structures](#references-to-unaligned-structures-)
             8. [DerefMove implementations](#derefmove-implementations-)
          4. [Unique immutable borrows in captures↵](#unique-immutable-borrows-in-captures)
          5. [Call traits and coercions↵](#call-traits-and-coercions)
          6. [Drop order↵](#drop-order)
       24. [Trait Object types](#11125-trait-object-types-)
       25. [Impl trait types](#11126-impl-trait-types-)
          1. [Anonymous type parameter](#anonymous-type-parameter-)
          2. [Abstract return types](#abstract-return-types-)
          3. [Abstract return types in trait declarations](#abstract-return-types-in-trait-declarations-)
             1. [Differences between generics and `impl Trait` in a return](#differences-between-generics-and-impl-trait-in-a-return-)
          4. [Impl trait limitations](#impl-trait-limitations-)
       26. [Inferred types](#11127-inferred-types-)
       27. [Vector types](#11128-vector-types-)
           1. [Custom vector support](#custom-vector-support-)
    2. [Dynamically sized types](#112-dynamically-sized-types-)
    3. [Nominal vs structural types](#113-nominal-vs-structural-types-)
    4. [Type layout](#114-type-layout-)
       1. [Size and Alignment](#1141-size-and-alignment-)
       2. [Primitive layout](#1142-primitive-layout-)
       3. [Unit and never type layout](#1143-unit-and-never-type-layout-)
       4. [Pointer and reference layout](#1144-pointer-and-reference-layout-)
       5. [Array layout](#1145-array-layout-)
       6. [Slice layout](#1146-slice-layout-)
       7. [String slice layout](#1147-string-slice-layout-)
       8. [Tuple layout](#1148-tuple-layout-)
       9. [Trait object layout](#1149-trait-object-layout-)
       10. [Closure layout](#11410-closure-layout-)
       11. [Bitfield layout](#11411-bitfield-layout-)
       12. [Layout representation](#11412-layout-representation-)
           1. [... representation](#-representation-)
              1. [Field priority](#field-priority-)
           2. [C representation](#c-representation-)
              1. [`repr(C)` structs and records](#reprc-structs-and-records-)
              2. [`repr(C)` unions](#reprc-unions-)
              3. [`repr(C)` field-less enums and enum records, and flags enums](#reprc-field-less-enums-and-enum-records-and-flags-enums-)
              4. [`repr(C)` enums and enum records with fields](#reprc-enums-and-enum-records-with-fields-)
           3. [Primitive representation](#primitive-representation)
           4. [Transparent representation](#transparent-representation-)
           5. [SAO (structure of array) representation](#sao-structure-of-array-representation-)
           6. [Additional layout modifiers](#additional-layout-modifiers-)
           7. [`align`](#align)
           8. [`packed`](#packed)
    5. [Interior mutability](#115-interior-mutability-)
    6. [Type coercions](#116-type-coercions-)
       1. [Coercion sites](#1161-coercion-sites-)
       2. [Coecion types](#1162-coecion-types-)
       3. [Unsized coercions](#1163-unsized-coercions-)
       4. [Least upper bound coercions](#1164-least-upper-bound-coercions-)
    7. [Destructors](#117-destructors-)
       1. [Drop scopes](#1171-drop-scopes-)
       2. [Scopes of function parameters](#1172--scopes-of-function-parameters-)
       3. [Scopes of local variables](#1173-scopes-of-local-variables-)
       4. [Temporary scopes](#1174-temporary-scopes-)
       5. [Operands](#1175-operands-)
       6. [Constant promotion](#1176-constant-promotion-)
       7. [Temporary lifetime extension](#1177-temporary-lifetime-extension-)
       8. [Extending based on patterns](#1178-extending-based-on-patterns-)
       9. [Extending based on expressions](#1179-extending-based-on-expressions-)
       10. [11.7.10. Not running destructors](#11710-not-running-destructors-)
12. [Generics](#12-generics-)
    1. [Generic parameters](#121-generic-parameters-)
       1. [Optional parameters](#1211-optional-parameters-)
    2. [Generics in functions](#122-generics-in-functions-)
    3. [Parameter packs](#123-parameter-packs-)
    4. [Where clauses](#124-where-clauses-)
    5. [Trait bounds](#125-trait-bounds-)
    6. [Return type bounds](#126-return-type-bounds)
    7. [Constraints](#126-constraints-)
       1. [Constraint functions \& methods](#1271-constraint-functions--methods-)
       2. [Constraint type aliases](#1272-constraint-type-aliases-)
       3. [Constraint constants](#1272-constraint-constants-)
       4. [Contraint properties](#1273-contraint-properties-)
       5. [Constraint disambiguation aliases](#1274-constraint-disambiguation-aliases-)
    8. [Generic arguments](#127-generic-arguments-)
    9. [specialization](#128-specialization-)
       1. [Internal specialization](#1281-internal-specialization-)
       2. [External specialization](#1282-external-specialization-)
          1. [Collisions](#collisions-)
          2. [Resolving Collisions](#resolving-collisions-)
       3. [Restrictions](#129-restrictions-)
          1. [Recursive dependencies](#recursive-dependencies)
          2. [Widening types](#widening-types)
13. [Metaprogramming](#13-metaprogramming-)
    1. [Meta variable](#131-meta-variable-)
    2. [Regular meta functions](#132-regular-meta-functions-)
    3. [Meta patterns](#133-meta-patterns-)
       1. [Parsed pattern meta-functions](#1331-parsed-pattern-meta-functions)
       2. [Matched pattern meta-functions](#1331-matched-pattern-meta-functions)
    4. [Meta attributes](#134-meta-attributes-)
    5. [Meta returns](#135-meta-returns)
       1. [Special meta return types](#1351-special-meta-return-types)
    6. [Meta-aliases](#136-meta-aliases)
    7. [Meta-function execution](#136-meta-function-execution)
    8. [Meta invocations](#136-meta-invocations-)
    9. [Meta hygiene](#137-meta-hygiene)
    10. [Meta utilities](#138-meta-utilities-)
        1. [`#tokenize`, `partial_synth` \& `#synthesize`](#1381-tokenize-partial_synth--synthesize-)
           1. [Meta blocks](#meta-blocks-)
        2. [`#pattern_parse`](#1382-pattern_parse-)
        3. [`#pattern_match`](#1383-pattern_match-)
        4. [`#invoke_repr`](#1384-invoke_repr-)
        5. [`#library` \& `#package`](#1385-library--package-)
14. [Operators](#14-operators-)
    1. [Operator items](#141-operator-items-)
       1. [Operator modifiers](#1411-operator-modifiers-)
          1. [`lazy`](#lazy-)
          2. [`chain`](#chain-)
          3. [`consume`](#consume-)
          4. [`by_ref`](#by_ref-)
       2. [Implementing operators on types](#1412-implementing-operators-on-types-)
       3. [Allowed characters.](#1413-allowed-characters-)
    2. [Special operators](#142-special-operators-)
       1. [Borrow operators](#1421-borrow-operators-)
          1. [Raw borrow operators](#raw-borrow-operators-)
       2. [Derefence operator](#1422-derefence-operator-)
       3. [Try operator](#1423-try-operator-)
          1. [Propagating try](#propagating-try-)
          2. [Unwrapping try](#unwrapping-try-)
       4. [Contract capture operator](#1424-contract-capture-operator-)
       5. [Logical boolean operators](#1425-logical-boolean-operators-)
       6. [catch operator](#1426-catch-operator-)
       7. [In-place operator](#1427-in-place-operator)
    7. [Core operators](#143-core-operators-)
       1. [14.3.1. Comparison operators](#1431-comparison-operators-)
          1. [`Equality`](#equality)
          2. [`Identity`](#identity)
          3. [`Ord`](#ord)
          4. [`TotalOrd`](#totalord)
        3. [14.3.3. Range operators](#1433-range-operators)
        4.  [14.3.4. Contains operator](#1434-contains-operator-)
        5. [14.2.5. Or-else operator](#1425-or-else-operator-)
        6. [14.3.6. Pipe operators](#1436-pipe-operators-)
        7. [14.3.7. Other operators](#1437-other-operators-)
        8. [14.4. Assginment operators](#144-assginment-operators-)
            1. [14.4.1. Basic assignment](#1441-basic-assignment-)
            2. [14.4.2. Destructuring assignment](#1442-destructuring-assignment-)
            3. [14.4.3. Compound assignment](#1443-compound-assignment-)
            4. [14.4.4. Core assignment Operators](#1444-core-assignment-operators-)
               1. [Other operators](#other-operators-)
        9. [Literal operators](#145-literal-operators-)
            1. [Literal operator item](#1451-literal-operator-item-)
            2. [Builtin literal operators](#1452-builtin-literal-operators-)
        15. [Operator scoping and use](#146-operator-scoping-and-use-)
15. [Precedence](#15-precedence-)
    1. [Built-in precedences](#151-built-in-precedences-)
    2. [User-defined precedence](#152-user-defined-precedence-)
       1. [Precendence order](#1521-precendence-order-)
       2. [Associativity](#1522-associativity-)
    3. [Precedence scoping and use](#153-precedence-scoping-and-use-)
16. [Visibility](#16-visibility-)
    1. [Specifiers](#161-specifiers-)
    2. [Common denominator](#162-common-denominator-)
17. [Attributes](#17-attributes-)
    1. [Built-in attributes](#171-built-in-attributes-)
       1. [Conditional compilation attributes](#1711-conditional-compilation-attributes-)
          1. [`cfg`](#cfg-)
          2. [`cfg_attr`](#cfg_attr-)
       2. [Derive attributes](#1712-derive-attributes-)
          1. [`derive`](#derive-)
          2. [`auto_derive`](#auto_derive-)
       3. [Macro attributes](#1713-macro-attributes-)
          1. [`meta_order`](#meta_order-)
       4. [Diagnostic attributes](#1714-diagnostic-attributes-)
          1. [`lint` attributes](#lint-attributes-)
             1. [Lint groups](#lint-groups-)
             2. [Tool lints](#tool-lints-)
          2. [`available` \& `unavailable`](#available--unavailable-)
          3. [`deprecated`](#deprecated-)
          4. [`obsolete`](#obsolete-)
          5. [`noasync`](#noasync-)
          6. [`must_use`](#must_use-)
          7. [`diagnostics`](#diagnostics-)
       5. [ABI, link, symbol, and FFI attributes](#1715-abi-link-symbol-and-ffi-attributes-)
          1. [`link_prefix`](#link_prefix-)
          2. [`link_suffix`](#link_suffix-)
          3. [`link_name`](#link_name-)
          4. [`link_ordinal`](#link_ordinal-)
          5. [`repr`](#repr-)
          6. [`export_name`](#export_name-)
          7. [`link_section`](#link_section-)
          8. [`no_mangle`](#no_mangle-)
          9. [`used`](#used-)
          10. [`callconv`](#callconv-)
          11. [`contextless`](#contextless-)
       6. [Code generation attributes](#1716-code-generation-attributes-)
          1. [`builtin`](#builtin-)
          2. [`inline`](#inline-)
          3. [`cold`](#cold-)
          4. [`track_caller`](#track_caller-)
          5. [`instruction_set`](#instruction_set-)
          6. [`opt_level`](#opt_level-)
          7. [`no_alias`](#no_alias-)
          8. [`bit_size`](#bit_size-)
          9. [`field_prioity`](#field_prioity-)
          10. [`val_range`](#val_range-)
          11. [`autoclosure`](#autoclosure-)
          12. [`spec_priority`](#spec_priority-)
          13. [`panic_handler`](#panic_handler-)
          14. [`safety_check`](#safety_check-)
          15. [Examples](#examples--9)
          16. [`fp_control`](#fp_control-)
              1. [`exceptions`](#exceptions-)
              2. [`rounding`](#rounding-)
              3. [`flush_to_zero`](#flush_to_zero-)
              4. [`flush_to_zero_half`](#flush_to_zero_half-)
              5. [`denormal_zero`](#denormal_zero-)
              6. [`precision`](#precision-)
              7. [`alt_half`](#alt_half-)
              8. [`nan_mode`](#nan_mode-)
              9. [`alt_handling`](#alt_handling-)
       7. [Module attributes](#1717-module-attributes-)
          1. [`path`](#path-)
       8. [Debug attributes](#1718-debug-attributes-)
          1. [`debugger_visualizer`](#debugger_visualizer-)
       9. [Documentation comments](#1719-documentation-comments)
          1. [`doc`](#doc-)
    2. [Tool attributes](#172-tool-attributes-)
    3. [User-defined attributes](#173-user-defined-attributes-)
18. [Implicit context](#18-implicit-context-)
    1. [Defining context](#181-defining-context-)
    2. [Internals](#182-internals-)
19. [Effect system](#19-effect-system-)
20. [Contracts](#20-contracts-)
    1. [Function contracts](#201-function-contracts-)
    2. [Asserts](#202-asserts-)
    3. [Contract groups](#203-contract-groups-)
    4. [Constact predicates](#204-constact-predicates)
    5. [Testing](#205-testing)
21. [ABI](#21-abi-)
22. [Configuration options](#22-configuration-options-)
    1. [`target_arch`](#221-target_arch-)
    2. [`target_feature`](#222-target_feature-)
       1. [x86/x64 (x86\_64)](#2221-x86x64-x86_64-)
       2. [arm/aarch64](#2221-armaarch64-)
       3. [riscv](#2223-riscv-)
    3. [`target_os`](#223-target_os-)
    4. [`target_endianness`](#224-target_endianness-)
    5. [`target_pointer_width`](#225-target_pointer_width-)
    6. [`compilation_mode`](#226-compilation_mode-)
    7. [`assertions`](#227-assertions-)
    8. [`panic`](#229-panic-)
23. [Illegal behavior](#23-illegal-behavior-)
    1. [Integer](#231-integer-)
       1. [Trunctation](#2311-trunctation-)
          1. [Examples](#examples)
       2. [Overflow/underflow ](#2312-overflowunderflow--)
       3. [Division by 0 ](#2313-division-by-0--)
    2. [Floating point](#232-floating-point-)
       1. [Illegal operations](#2321-illegal-operations-)
       2. [Floating-point to integer out-of-bounds](#2322-floating-point-to-integer-out-of-bounds-)
          1. [Examples](#examples-1)
    3. [Memory](#233-memory-)
       1. [Out-of-bounds](#2331-out-of-bounds-)
       2. [Incorrect pointer alignment](#2332-incorrect-pointer-alignment-)
       3. [Sentinel access](#2333-sentinel-access-)
24. [Language builtins](#24-language-builtins-)
    1. [Traits](#241-traits-)
       1. [`Clone`](#2411-clone-)
       2. [`Copy`](#2412-copy-)
       3. [`Equality`](#2413-equality-)
       4. [`Identity`](#2414-identity-)
       5. [`Ord`](#2415-ord-)
       6. [`TotalOrd`](#2416-totalord-)
       7. [`StructuralEquality`](#2417-structuralequality-)
       8. [`StructuralIdentity`](#2418-structuralidentity-)
       9.  [`OptAccess`](#2419-optaccess-)
       10. [`DispatchFromDyn`](#24110-dispatchfromdyn-)
       11. [`From`](#24111-from-)
       12. [`TryFrom`](#24112-tryfrom-)
       13. [`UnwrapFrom`](#24113-unwrapfrom-)
       14. [`Default`](#24114-default-)
       15. [`Index`](#24115-index-)
       16. [`IndexMut`](#24116-indexmut-)
       17. [`OptIndex`](#24117-optindex-)
       18. [`OptIndexMut`](#24118-optindexmut-)
       19. [`RevIndex`](#24119-revindex-)
       20. [`RevIndexMut`](#24120-revindexmut-)
       21. [`RevOptIndex`](#24121-revoptindex-)
       22. [`RevOptIndexMut`](#24122-revoptindexmut-)
       23. [`Fn`](#24123-fn-)
       24. [`FnMut`](#24124-fnmut-)
       25. [`FnOnce`](#24125-fnonce-)
       26. [`IntoIterator`](#24126-intoiterator-)
       27. [`Iterator`](#24127-iterator-)
       28. [`Try`](#24128-try-)
       29. [`TryUnwrap`](#24129-tryunwrap-)
       30. [`Catch`](#24130-catch-)
       31. [`Hash`](#24131-hash-)
       32. [`Sized`](#24132-sized-)
       33. [`Unsize`](#24133-unsize-)
       34. [`CoerceUnsized`](#24134-coerceunsized-)
       35. [`Drop`](#24135-drop-)
       36. [`Borrow`](#24136-borrow-)
       37. [`BorrowMut`](#24137-borrowmut-)
       38. [`Deref`](#24128-deref-)
       39. [`DerefMut`](#24139-derefmut-)
       40. [`DerefMove`](#24140-derefmove-)
       41. [`LogicalAnd`](#24141-logicaland-)
       42. [`LogicalOr`](#24142-logicalor-)
       43. [`ResultBuilder`](#24144-resultbuilder-)
    2. [Types](#242-types-)
       1. [`Option`](#2421-option-)
       2. [`Result`](#2422-result-)
       3. [`KeyValue`](#2423-keyvalue-)
       4. [`KeyPath`](#2424-keypath-)
       5. [`ManuallyDrop`](#2425-manuallydrop-)
       6. [`MaybeUninit`](#2426-maybeuninit-)
       7. [`InterplatedString`](#2427-interplatedstring-)
       8. [`meta.TokenStream`](#2428-metatokenstream-)
       9. [`meta.FragmentStream`](#2429-metafragmentstream-)
       10. [`meta.Ast`](#24210-metaast-)
       11. [`meta.AttribData`](#24211-metaattribdata-)
    3. [Precedences](#243-precedences-)
    4. [Meta-functions](#244-meta-functions-)
       1. [`line`](#2441-line-)
       2. [`column`](#2442-column-)
       3. [`group`](#2443-group-)
       4. [`package`](#2444-package-)
       5. [`library`](#2445-library-)
       6. [`file`](#2446-file-)
       7. [`context`](#2447-context-)
       8. [`embed`](#2448-embed-)
       9. [`asm`](#2449-asm-)
       10. [`tokenize`](#24410-tokenize-)
       11. [`partiah_synth`](#24411-partiah_synth-)
       12. [`synthesize`](#24411-synthesize-)
       13. [`pattern_parse`](#24412-pattern_parse-)
       14. [`pattern_match`](#24413-pattern_match-)
       15. [`invoke_repr`](#24414-invoke_repr-)
       16. [`meta.library`](#24415-metalibrary-)
       17. [`meta.package`](#24416-metapackage-)
25. [Main function](#25-main-function-)
26. [Preludes](#26-preludes-)
    1. [`core` prelude](#263-core-prelude-)
    2. [`std` prelude](#262-std-prelude-)
    3. [Custom preludes](#263-custom-preludes-)
27. [Panic](#27-panic-)
    1. [Panic handler](#271-panic-handler-)
       1. [Standard behavior](#2711-standard-behavior-)
    2. [ Unwinding](#272-unwinding-)
       1. [Unwinding over FFI boundaries](#2721-unwinding-over-ffi-boundaries-)
       2. [Function unwinding linkage](#2722-function-unwinding-linkage-)
28. [Unsafety](#28-unsafety-)


# 1. Introduction [↵](#tables-of-contents)

This file contains the current language design for the Minoa language, and may optionally include rationals for design decisions.
This is not a full specification, as the final specification will be derived from this design once the langauge reaches v1.0.

This documentation is an overview of the Minoa language in its current state, and is written for the development of the langauge and those who are interested in the langauge.

## This document is provisional [↵](#1-introduction-)

The contents of this document is still provisional and is subject to change at any time.
This means the syntax, languages rules, core and standard libary, compiler infrastructure, package manager/build tool, and other aspect of the design that have not been decided on yet.
This therefore will contain gaps for parts that have not been decided on yet.
There may also be unclear language within this document that still needs to be refined during the development process.

In addition, the current name 'Minoa' is a work in progress (W.I.P.) name and may also still change in the future.

## Notation [↵](#1-introduction-)

The notation used in the design documents can be found within the [Notation section of the combined grammar](grammar.md#notation)

# 2. Source code representation [↵](#tables-of-contents)

This section contains info about the source code representation in the file, and by extension about the data on disk.

## 2.1. Input format [↵](#2-source-code-representation-)

Each source input is interpreted as a sequence of Unicode codepoints encoded within the utf-8 format.
If an input does not contain a valid utf-8 sequence, this will result in an error.

Source input is generally represented as a sequence of bytes in a file, but can also come from other sources, some examples of which are:
- an interactive programming enviornment (a so-called "Read-Evaluate-Print-Loop" or REPL),
- a database
- a memory buffer of an IDE
- command-line arguments
- etc.

Minoa source files use the extension `.mn`

## 2.2. Byte order markers [↵](#2-source-code-representation-)

```
<byte-order-marker> := "\xEF\xBB\xBF"
```

The file may begin using a byte order marker, this marker is kept track of, but is generally ignored by the compiler.
The utf-8 byte order marker does not encode the order, as utf-8 work in single byte units and can therefore not be in a different marker.
It is mainly there to indicate the that content of this file encodes a utf-8 sequence, preventing it to be interpreted as another text encoding.

If the file would be reconstructed from its lexical representation, the file will be rebuilt to include the byte order marker if it was present before.

The utf-8 byte order marker is the following: `EF BB BF`.

Any other byte order marker is invalid and will produce an error, as the text file would represent another text encoding.
The disallowed byte order markers are the following:

Encoding    | Representation
------------|----------------
utf-16 (be) | FE FF
utf-16 (le) | FF FE
utf-32 (be) | 00 00 FE FF
utf-32 (le) | FF FE 00 00
utf-7       | 2B 2F 76
utf-1       | F7 64 4C
utf-ebcdic  | DD 73 66 73
scsu        | 0E FE FF
bocu-1      | FB EE 28
gb18030     | 84 31 95 33

## 2.3. Shebang [↵](#2-source-code-representation-)

```
<shebang> := '#!' ? any valid character ? <newline>
```

A file may contain a shebang in the first line in a file, but will be ignored (and preserved) by the compiler.

## 2.4. Normalization [↵](#2-source-code-representation-)

Source files are normalized using the Normalization Form C (NFC) as defined in [Unicode Standard Annex #15](https://www.unicode.org/reports/tr15/tr15-56.html).
It is generally expected that the source code is stored within a normalized form.
As this cannot be guaranteed, the compiler will automatically try to convert unnormalized text into normalized text.
In case a non-normalized character sequence is detected, a warning will be emitted.

If the input is guaranteed to be normalized, this can be turned off.

An exception to normalization is [string literals](#64-string-literals-), in which no normalization happens.

> _Note_: Normalization Form D is more uniform, in that characters are always maximally decomposed into combining characters; in NFD, characters may or may not be decomposed depending on whether a composed form is available. NFD may be more suitable for certain uses such as type correction, homoglyps detection, or code completion. But NFC is also way more common and recomended in locations, it's also more commonly used for languages that support unicode.

> _Todo_: We might be able to also add support for homoglyph and confusables detection and convert them to a single character, see ['Confusable Detection' section of Unicode Annex #39](https://www.unicode.org/reports/tr39/#Confusable_Detection)
> _Todo_: A quick detection optimization is defined within the ['Quick Check for NFC' section in Unicode Annex #15](https://unicode.org/reports/tr15/#NFC_QC_Optimization)

# 3. Lexical structure [↵](#tables-of-contents)

This section contains information about the lexical structure of a code file, which will be interpreted as tokens.

A token is a primitive in the gramar of a language. Tokens are produces from the incoming source file.
The supported tokens are defined below.
In addition to these, literals are also interpreted as tokens, but infomation about them is located within their own [section](#6-literals-).

> _Note_: The langauge is designed in such a way, that each line can independtly be parsed into a set of tokens without needing to have any context from the surrounding lines

## 3.1. Whitespace [↵](#3-lexical-structure-)

Whitespace is uses to determine how lexical elements withing a file are interpreted, and used to
- separate seperate lexical elements
- decide how a mix of pre/post/in-fix operator is interpreted.

For any other purpose, whitespace is essentially ignored.
All whitespace is preserved in any reconstructed file.

Below are lists of all unicode characters recognized as either horizontal or vertical whitespace:
- Horizontal whitespace:
  - U+0009 CHARACTER TABULATION (horizontal tab / HT)
  - U+0020 SPACE
  - U+200E LEFT-TO-RIGHT MARK
  - U+200F RIGHT-TO-LEFT MARK
- Vertical whitespace:
  - U+000A: LINE FEED (newline / LF)
  - U+000B: LINE TABULATION (vertical tab / VT)
  - U+000C: FORM FEED (page break / FF)
  - U+000D: CARRIAGE RETURN (CR)
  - U+0085: NEXT LINE (unicode newline)
  - U+2028: LINE SEPARATOR
  - U+2029: PARAGRAPH SEPARATOR

A program has identical meaning if each whitespace element is replaces with any other legal whitespace character, such as a single space.
This does not include newline sequences which are mentioned below.

> _Note_: This is **not** a direct mapping to the unicode separator category `Z`

### 3.1.1. New line sequences [↵](#31-whitespace-)

```
<new-line> := [ "\r" ] "\n"
```

New line sequences count as whitepace, but are handled differently.
New line have special meaning in the following cases:
- when ending a comment line
- when ending a multi-line string segment

A newline within the file is represented using a newline sequence `\n` (U+000A).
This may also be preceded by a carriage return `\r` (U+000D), any other occurance of a carriage return is ignored by the compiler, but must be retained, meaning that carriage returns will be preserved in any reconstructed file.

Tooling is required to keep the original line ending when not specified otherwise.

When specifified, tooling may:
- convert all line ending to either `\n` or `\r\n` when specified, or
- remove all occurances of `\r` when not part of a line ending when specified

## 3.2. Keywords [↵](#3-lexical-structure-)

Keywords represent names within the code that have a special meaning in the language, such as declaring a function.

There are 3 types of keywords:
- strong
- reserved
- weak

### 3.2.1. Strong keywords [↵](#32-keywords-)

A strong keyword is a keyword that always has a meaning, regardless of where in the code it is located, and can therefore not be used for anything else.
A list of strong keywords can be found below (in a close to alphabetic order):
```
as
as?
as!
assert
async
await
b8
b16
b32
b64
bitfield
bool
break
catch
char
char7
char8
char16
char32
const
constraint
continue
cstr
defer
do
dyn
else
enum
errdefer
f16
f32
f64
f128
false
fallthrough
fn
for
i8
i16
i32
i64
i128
if
in
!in
impl
is
!is
isize
let
loop
match
mod
move
mut
pub
ref
return
self
static
str
str7
str8
str16
str32
struct
throw
trait
true
try
try!
type
opaque
u8
u16
u32
u64
u128
union
unsafe
use
usize
while
when
where
yield
```

### 3.2.2. Reserved keywords [↵](#32-keywords-)

A reserved keyword is keyword that is not currently used, but has been set aside as not being possible to be used by the langauge for future use.
A list of reserved keywords can be found below (in a close to alphabetic order):
```
```

### 3.2.3. Weak keywords [↵](#32-keywords-)

A weak keyword is a keyword that is dependent on the surrounding context and can be used anywhere outside
A list of weak keywords can be found below (in a close to alphabetic order):
```
accessor
adapt
alias
align
allowzero
assign
associativity
attr
chain
consume
derive
did_set
distinct
extend
flag
full
get
higher_than
infix
init
invar
lazy
lib
literal
lower_than
member
member_attr
meta
opaque
override
package
peer
post
postfix
pre
precedence
prefix
property
raw
record
sealed
set
sparse
super
template
tls
unique
volatile
will_set
```

## 3.3. Names [↵](#3-lexical-structure-)
```
<start-letter> := '_' | ? unicode category XID_Start ? | ? unicode category Nd ?
<letter> := ? unicode category XID_Continue ?

<name> := <start-letter> <letter>*
```

A name is sequence of unicode points that can be used to identify a symbol or value in code.
Unicode support allows some of the following names:
- `foo`
- `_identifier`
- `Москва`
- `東京`
 

Unlike most langauges, names are allowed to start with a number, as we do not support direct postfixes on numbers ('e'/'E' are special cases though).
The main rule is, that as long as a name does not also match either a keyword or a literal, it is a name.

Allowing names to start with a digit, can allow uses like:
```
enum Dimension {
    2D,
    3D,
}
```

Zero width non-joiner(ZWNJ U+200C) and zero width joiner(ZWJ U+200D) are not allowed in names.

> _Note_: Names starting a double underscore '__' are reserved for the compiler or a runtime, user should not use any of these names, as this may cause issues when interal names have the same value.

> _Todo_: Add ASCII only restrictions when needed.

## 3.4. Punctuation [↵](#3-lexical-structure-)

```
<punct> := ? any unicode point within , except for <reserved-punct> ?
<reserved-punct> := <delimiters> | "'" | '"'
```

Punctuation are sequences of character that cannot be interpreted as any other token.
There is no fixed set of allowed punctuation.

There is also a set of characters that are not allowed in punctuation, as they each have their own special meaning.
These are `'` and `"`.

> _Todo_: Limit possible punctuation symbol to fix set of common, but distinguishable symbols

## 3.5. Delimiters [↵](#3-lexical-structure-)
```
<delimiters> := <opening-delimiters> | <closing-delimiters>
<opening-delimiters> := '{' | '(' | '['
<closing-delimiters> := '}' | ')' | ']'
```

Delimiters are special forms of punctuation that are used to surround an inner subsection of code.
An opening delimiter always needs to be paired with a closing delimiter.

There are 3 types of delimiters:
Delimiter   | Type
------------|----------------
`{` and `}` | (Curly) braces
`[` and `]` | (Square) brackets
`(` and `)` | Parentheses

## 3.6. Comments [↵](#3-lexical-structure-)

Comments are used to add additional info or documentation to code.

Only line comments are supported, these begin at a given token depending on the type of comment, and complete at the end of the current line.
The decision was made not to support block comments, as nestable block comments complicate parsing, and they can cause some edgecases where the end token can appear within a string inside of the comment.

There are 2 type of comments:

### 3.6.1. Regular comment [↵](#33-comments-)

```
<regular-comment> := '//' {? any unicode character ?}* <new-line>
```

Regular comments are used to just add additional info to code, or used to comment out code, allowing the code is still be in the file, but interpreted as a comment.

Some elements that are in doc comments are also available in a regular comment and can be uses by tooling, but these will not end up in generated documentation.
These elements are:
- [General notes & exhortations](#general-notes--exhortations-)
- [Issue](#issue-)

In addition, a regular comment only element is also supported: a mark.
A mark is used for easier code navigation in tools that support it.
A mark is always provided in a comment as `// \mark name`.

Regular comments are stored as metadata associated with tokens and are not tokens by themselves.

### 3.6.2. Doc comments [↵](#33-comments-)

```
<doc-comment> := <doc-comment-start> {? any unicode character ?}* <new-line>
<doc-comment-start> := '///' | '//!'
```

Doc(umentation) comments are used to provide documentation for a given item.

In addition to block and line versions, documentation comments can also either apply to:
- the item directly below them, just called 'regular doc comments', or
- if a module is in its own file, to the module they are in, called 'top-level doc comments',

These are differentiated by how the comment starts, regular doc comments start with 3 forward slashes, i.e. `///`, while top-level doc comments start with 2 forward slashed, followed by an exclaimation mark, i.e. `//!`

A top-level doc comment within the library's root module also apply directly to the library.

During parsing, these get converted to their relavent attributes, i.e.
- regular doc comments like `/// Foo` and `/** Foo */` map to `@doc("Foo")`
- module-level doc comments like `//! Bar` and `/*! Bar */` map to `@!doc("Bar")`

A carriage return (CR) is not allowed within a doc comment, except when followed immediately by a newline.

Regular doc comments are only allowed before items, and module-level doc comments are only allowed before any item within a module, anything else will cause an error.

#### `doc` attribute [↵](#332-doc-comments-)

Since doc comment map into a `doc` attribute, all elements within the format described above are converted to a doc attribute.
This attribute may be split up into a sequence of smaller `doc` attributes. Each separate attribute will be interpreted as its own separate line, meaning that:
```
@doc("This")
@doc("does")
@doc("a thing")
```
will be equivalent to
```
//! This
//! does
//! a thing
```

A `doc` attribute may only contain a string literal, or a set of specific arguments

Whenever a `doc` attribute does not have a specific section specified, and only contains a string, of a known set of doc attributes.

The below section also explains the mapping between a given doc element and it's associated sub-attribute.

> _Note_: It's generally prefered to use comments instead of explicit `doc` attributes where possible for readability

#### Doc comment format [↵](#332-doc-comments-)

Doc comments follow a specific format, which starts on either the first line doc comment, or the first or second line of a block doc comment (which ever is the first to contain text)

Doc comments generally don't follow a specific format, except that the first line of text before the first v

The general format is the following:
```
/// [ Short description what the item is/does ]
///     
/// [ Receiver ]
/// [ Implicit parameters ]
/// [ Parameters ]
/// [ Return ]
/// 
/// [ Long description what the item is/does ]
```

Each part of the documentation is known as a doc element, which with the exception of the short and long description, must start at the beginning of a line and is similar to escape code, but where the text following the backslash (`\`) defined the element.

Some elements allow additional data to be provided later on in the line, by starting them with a `\`, or as values in a comma-separated list.
Check each element for info about what additional values they support.

Doc comments will trim the start based each line based on the depth the first character on the first line with text is located. This means that if a lower line starts on an earlies column, an error will be generated.

Support of some elements will depend on the viewer used (e.g. browser).

> _Note_: Any 3rd party document generation tooling (doc-gen) is expected to follow this specification.

> _Note_: Additional elements may be added later

##### Short description [↵](#doc-comment-format-)

The short description is generally a simple 1 line sentence describing the basic function of an item.

It is mapped to the `short` sub-attribute, i.e. `@doc(short="short desciption here")`

##### Long description [↵](#doc-comment-format-)

The long description is a detailed description describing the functionality of an item.

A long description follows a flavor of the .md/mark-down spec, but with some additional features to aid in the generation of documentation.
These elemens will be explained further down below.

This element is created when either no sub-attribute is used, or a value is given to the `long` sub-attribute, i.e. `@doc("long description here")` or `@doc(long="long description here")`

##### Including external text [↵](#doc-comment-format-)

Whenever text is stored in an external file to the comment, this can be included using a special `include_str` element.
It may also be passed to a `doc` (sub-)attribute where a string literal is allowed.

the syntax of this element is either: `include_str(source_file)`, where `source_file` is itself a string literal with relative path pointing to the file that contains the text.
This path is relative to the file in which the documentation is located.

Text may be inserted anywhere inside of text by surrounding the element with `\{` and `}`

For example:
- `@doc(include_str("./example.md"))`
- `@doc(... = include_str("./example.md"))`
- `/// some text in \{include_str("./example.md")}`

##### Item links [↵](#doc-comment-format-)

An item link is an extension of markdown links, allowing the links to refer to a specific item within code.
These links may refer to any item accessable from the scope the comment is in, using their paths, including `Self`, `self`, `super`, `lib`, and `package`.
The links are relative to the scope they are located in.

Links may also refer to builtin types.

> _TODO_: Add example based on std lib

URL fragments are also allowed

> _TODO_: Add example based on std lib, pointing to a segment, i.e. path#segment

When linking a function, they have to be differentiated using their parameter labels, i.e. ``[`foo()`]`` and ``[`foo(_:_)`]`` are distinct.

##### Receiver [↵](#doc-comment-format-)

The receiver elements allows additional info about a receiver to be added.

Receiver info can be provided in a comment as `/// \receiver {info}`, or in a sub-attribute as `@doc(receiver="{info}")`.

The info can be split across multiple lines by having the following lines indented.

##### Inferred parameter [↵](#doc-comment-format-)

The inferred parameter element allows additional info about an inferred parameter to be added.

Inferred parameter info can be provided in a comment as `/// \infer_param {name} {info}`, or in a sub-attribute as `@doc(infer_param(name="{name}", info="{info}"))`

The info can be split across multiple lines by having the following lines indented.

##### Parameter [↵](#doc-comment-format-)

The parameter element allows additional info about a parameter to be added.

Parameter info can be provided in a comment as `/// \param {name} {info}` or in a sub-attribute as `@doc(param(name="{name}", info="{info}"))`.

Information about the default value can in addition be provided in a comment as `\default {info}`, or in a sub-attribute as `@doc(param(..., default=""))`.

The info can be split across multiple lines by having the following lines indented.

##### Return [↵](#doc-comment-format-)

The return element allows additional info about a item's return type to be added.

Return info can be provided in a comment as `/// \return {info}`, or in a sub-attribute as `@doc(return="info")`.

The info can be split across multiple lines by having the following lines indented.

##### Named return [↵](#doc-comment-format-)

The named return element allows additional info about a item's return to be added if it contains named return values.

Return info can be provided in a comment as `/// \named_return {name} {info}`, or in a sub-attribute as `@doc(named_return(name="{name}", info="info")`.

The info can be split across multiple lines by having the following lines indented.

##### Since [↵](#doc-comment-format-)

The since element allows the version of the library since when an item was added to be specified.

Since info is set in a comment as `/// \since {version}`, or in a sub-attribute as `@doc(since="{version}")`

##### Pre-condition contract [↵](#doc-comment-format-)

The pre element allows additional info about an item's pre-condition contract to be added.

Pre-condition info can be provided in a comment as `/// \pre {info}`, or in a a sub-attribute as `@doc(pre={info})`.

The info can be split across multiple lines by having the following lines indented.

##### Post-condition contract [↵](#doc-comment-format-)

The post element allows additional info about an item's post-condition contract to be added.

Post-condition info can be provided in a comment as `/// \post {info}`, or in a a sub-attribute as `@doc(pre={info})`.

The info can be split across multiple lines by having the following lines indented.

##### Invariant contract [↵](#doc-comment-format-)

The pre element allows additional info about an item's invariant contract to be added.

Invariant info can be provided in a comment as `/// \invar {info}`, or in a a sub-attribute as `@doc(invar={info})`.

The info can be split across multiple lines by having the following lines indented.

##### Complexity [↵](#doc-comment-format-)

The complexity element allows additional info about an item's complexity to be added.
Complexity is generally written in a big O notation.

Complexilty info can be provided in a comment as `/// \complexity {info}`, or in a sub-attribute as `@doc(complexity="{info}")`.

##### Important [↵](#doc-comment-format-)

The important element allows additional info about what is important when using an item. This info will be clearly marked in the documentation.

Important info can be provided in a comment as `/// \important {info}`, or in a a sub-attribute as `@doc(important="{info}")`.

The info can be split across multiple lines by having the following lines indented.

##### Warning [↵](#doc-comment-format-)

The warning element allows additional info about what a user should care to avoid when using an item. This info will be clearly marked in the documentation.

Warning info can be provided in a comment as `/// \warning {info}`, or in a a sub-attribute as `@doc(warning="{info}")`.

The info can be split across multiple lines by having the following lines indented.

##### Attention [↵](#doc-comment-format-)

The attention element allows additional info about what a user should pay attention to when using an item.
This is more general then important and warning, which specifically state what is important when using the item, and what should be avoided, respectively.
This is therefore also not hightlighted like those 2 elements are.

Warning info can be provided in a comment as `/// \attention {info}`, or in a a sub-attribute as `@doc(attention="{info}")`.

The info can be split across multiple lines by having the following lines indented.

##### Errors [↵](#doc-comment-format-)

The errors element can be in one of two forms:
- a general overview of all possible errors that can be returned, or
- info a specific error that's in the possible errors

This is mainly useful in conjunction with a `Result` return type.

General error info can be provided in a comment as `/// \errors {info}`, or in a sub-attribute as `@doc(errors="{info}")`.

While providing info about a specific error, it can be provided in a comment as `/// \error {name} {info}`, or in a sub-attribute as `@doc(error(name="{error}", info="{info}"))`.
The `{error}` value will refer to a specific variant of the return error, while `{info}` is optional, and when left out, it will add the short description of the variant specified in `{error}`.

The info can be split across multiple lines by having the following lines indented.

##### Issue [↵](#doc-comment-format-)

The issue element allows an issue to be associated with an item.

An issue can be provided in a comment as `/// \issue {issue}`, or in a sub-attribute as `@doc(issue={issue})`.
When `{issue}` is just a number, it will generated a link based on the [issue tracker base](#issue-tracker-base-), otherwise an issue can be provided with a URL to the specific issue.

##### General notes & exhortations [↵](#doc-comment-format-)

This is a collection of elements with commonality between them, as they are meant to communicate additional info about code between authors and/or users.

All of these elements follow the same notation, with an additional author or authors.
These can be provided in a comment as `/// \{elem} {info}` or `/// \{elem}({author}) {info}`, or in a sub-attribute as `@doc({elem}="{info}")` or `@doc({elem}(authors={authors}, info={info}))`.

Authors are a comma separated list in a comment, and either a string or array literal of strings in a sub-element, depending if there is 1 or multiple authors.
Each author follows the following format `name < example@example.com >`, where `name` is the authors username and `< example@example.com >` is replaced by the mail associated with the author, surrouneded by `<>`, the mail is entirely optional.

The possible elements are the following:
- `bug`: Adds additional info about a bug in the item/code and any authors associated with it
- `experimental`: marks an item/code as experimental, with optional info and authors that are relevant to it.
                  If no info needs to be added, it can be simply provided in a comment as `/// \experimental`, or in a sub-attribute as `@doc(experimental)`
- `note`: Adds additinal info about a given item/code
- `remark`/`remarks`: Adds remarks/criticism about a given item/code
- `todo`: Adds info about additional work that needs to be done on the item/code
- `perf`: Adds additional remarks abour the performance of an item/code

##### Favicon [↵](#doc-comment-format-)

The favicon element specifies a favicon used for the documentation.
By default, no favicon will be set.
A favicon is only allowed in a doc comment applying to the root of a library.

A favicon may point to either a url or a local path, when pointing to a local path, the favicon will be included in the generated documentation.

Favicon are set in a comment as `//! \favicon {path}`, or in a sub-attribute as `@!doc(favicon="{path}")` or `@!doc(favicon("{path}"))`.
To explicitly indicate what the path is in a sub-attribute, `favicon(path = "{path}")` can be used instead.

In addition, a favicon may also specify the sizes contained within the file pointed to by the given path. 
It can be added using `, sizes="{sizes}"`, in a sub-attribute this will look like `@!doc(favicon("{path}", sizes="{sizes}"))`.
Where `{sizes}` is a space separated list of `<width>x<height>` values, with `<width>` and `<height>` being values ranging from 1 to 256, e.g. "32x32 64x64".

The type of the icon will be derived from the file extension of a given path, which can be one of the following:
- `.ico`, corresponding to `type="image\x-icon"`
- `.gif`, corresponding to `type="image\gif"`
- `.png`, corresponding to `type="image\png"`
- `.svg`, corresponding to `type="image\svg+xml"`

A favicon will correspond to the following html: `<link rel="icon" type="{}" sizes="{sizes}" href="{path}">`, where `sizes` will be left out when none are specified, and the values between `{}` are replaced with their respective values.

If multiple favicons are specified, the viewer decided which of the favicons is used.

##### Logo [↵](#doc-comment-format-)

The logo element specifies the logo used within the documentation.
By default, no logo will be et.
A logo is only allowed in a doc comment applying to the root of the library.

A logo may point to either a url or a local path, whe npointing to a local path, the favicon will be included in the generated documentation.

Logos are set in a comment as `//! \logo {path}` or in a sub-attribute as `@!doc(logo="{path}")` or `@!doc(logo("{path}"))`.
To explicitly indicate what the path is in a sub-attribute, `logo(path = "{path}")` can be used instead.

In addition, a logo may also specify the size is should show up as. This can be done by specifying a width and/or height it should show up as.
This will not un-uniformly scale the image to make both the width and height to match if the provided log does not have the same aspect ratio, but will instead uniformly scale the image until either the width or the height have hit the desired size.
They can be added using `, width={width}` and `, height={height}`, in a sub-attribute, this will look like `@!doc(logo("{path}", width={width}, height={height}))`

Supported image types are:
- PNG
- APNG
- GIF
- JPEG
- SVG
- WebP

A logo will correspond to the following html: `<img src='{path}' alt='logo' width='{width}'>`, if `width` will be defaulted to the width of thelogo when not explicitly specified, and the values between `{}` are replaced with their respective values.

If multiple logos are specified, an error will be generated.

> _Note_: the specifics of how the logo will be located in the docs still needs to be determined, including the maximum size

##### Playground [↵](#doc-comment-format-)

The playground element specifies how code in the documentation may be run using a button within a code block.
By default, no playground is used, ano no buttons allowin execution will show up.
A playgound is only allowed in a doc comment applying to the root of a library.

The playground can be set in comments as `//! \playground {playground}` or in a sub-attribute as `@!doc(playground({playground}))`.
Where `{playground}` can be either:
- `embedded` which will run the code in a documentation provided playground, or
- `url={path}` which will run the code in an online playground

##### Issue tracker base [↵](#doc-comment-format-)

The issue tracker base defines the base URL which can be uses by issue element to construct a url from the issue number.
By default, no url is assigned and issue elements must declare a full path.
An issue tracker base is only allowed in a doc comment applying to the root of the library.

The issue tracker base can be set in comments as `//! \issue_tracker_base {url}` or in a sub-attribute as `@!doc(issue_tracker_base="{url}")`.

##### No source [↵](#doc-comment-format-)

By default, the docs will include the source code, adding links for each item to the relavent source code.
Using this element, this can be turned off, meaning that source code won't be included and no links to it will be generated.

This element can be applied to the library as a whole, or to specific items or modules.

No source is set in a comment as `/// \no_source` or in an attribute as `@doc(no_source)`.

##### `inline` and `no_inline`

The inline attribute are applied to `use` statements and control how documentation shows up. The overwrite the default behavior set when generating documentation.

This can only be specified as a sub-attribute, which is defined as `@doc(inline)` or `@doc(no_inline))`.

Assuming the following code:
```
// (1)
pub use bar::Bar;

/// Docs for the 'bar' module
mod bar {
    /// Docs for the 'Bar' struct
    pub struct Bar;
}
```

If no attribute appears at '(1)', then the page will add `pub use bar::Bar` to its `use` section, which will have a link to the item within said module.
If instead the `@doc(inline)` attribute is added, the `use` will not appear with the `use` section and the documentation will be generated directly within the documentation for the surrounding module, instead of on its own page.
This does mean the documentation for `bar` will located in the module's page, only the docs for the struct `Bar`.

If instead `bar` would be private, as in the following code:
```
// (1)
pub use bar::Bar;

/// Docs for the 'bar' module
mod bar {
    /// Docs for the 'Bar' struct
    pub struct Bar;
}
```
`Bar`'s documentation would be generated within the module's page when the doc-gen is specified to generate documentation for publicly accessable items that are located inside private items.
In this case, the `no_inline` attribute will add `Bar` within the `use` section, but will not link to anything, as the containing module is private, assuming the doc-gen is not told to generate docs for private items.

##### `hidden` [↵](#doc-comment-format-)

Any item that is annotated with this attribute will be hidden from the documentation, unless the doc-gen is told to generate hidden docs.

This can only be specified as a sub-attribute, which is defined as `@doc(hidden)`

##### `alias` [↵](#doc-comment-format-)

An alias element specifies an alias when searching for a given item, meaning that searching for this alias will result in the item the comment applies to. Using the actual name of the item still works.

An alias is set in a comment as `//! \alias {name}` or in a sub-attribute as `@doc(alias="name")`.

A use case for this can be seen in the following code:
```
pub struct Foo {
    ...
}

impl Foo {
    pub fn do_ffi_thing(&mut self) -> i32 {
        unsafe { ffi::lib_name_act_function(...) }
    }
}
```
The function is now wrapped for convencience, but now searching for `lib_name_act_function` will not result in any function within the documentation.
Adding the alias element `@doc(alias="lib_name_act_function")` to it allows the item to be looked up using the original name of the underlying function, and the user will fine the correct method to use.

> _Note_: This example assumes that `ffi` is not a documented module.

### 3.6.3. Examples [↵](#33-comments-)

Below are some examples of how doc comments can be used.

> _Note_: Not all possible elements are shown in these examples

> _Todo_: Add more examples

#### Library description [↵](#363-examples-)

By placing comments in the library's root module, we can have these comments specify the description for the library

```
//! Short description for the libary
//!
//! Long description for the library (as we are in the root module)
//!
//! \favicon path/favicon.ico, sized="32x32 64x64"
//! \logo path/logo.png
//! \playground embedded
// or
//! \playground https://www.example.com/playground
//! \issue_tracker_base https://www.example.com/repo.git/issues
```
or alternatively, this can also be
```
@!doc(short="Short description for the library")
@!doc("")
@!doc("Long description for the library (as we are in the root module)")
@!doc(favicon=(path=path.favicon.ico, sized="32x32 64x64"), logo="path/logo.png")
```

#### Documenting a module [↵](#363-examples-)

When a module is in its own file, we can use top-level comment:
```
//! Short description of the module that is represented by the current file.
```

But when we are in a nested module, we need to put regular doc comments before them:
```

/// Description of `foo`.
mod foo {
    //! This will result in an error, as we aren't at the top level of the current file.
}
```
This is simila for doc attributes
```
@doc("A description for `foo`")
mod foo {
    @!doc("This will also result in an error")
}
```


# 4. Package structure [↵](#tables-of-contents)

Additional info can be found in [the package design](packages.md).

## 4.1. Packages [↵](#4-package-structure-)

A package represents the upper level of a hierarchy of artifacts and the main unit of distribution.

Packages themselves are not the direct result of compilation, but play an integral part in code organization.
Howevver, packages do define how code is imported, as it is the base on which all import paths are based..

A package can contain any number of artifacts, allowing related code to be shared as a single unit,
meaning that if a project is split up in modularized components, they can still be easily distributed, without having to result to sub-naming.

### 4.1.1. Groups [↵](#41-packages-)

Packages may also be part of a group, this is a logical grouping of packages, but unlike packages, they are an optional part of code distribution.
The main purpose of groups is allowing an organization combine their packages under the organization's name.
This also allows muliple organizations that each have their own group, to have similar package name.

An example of how groups can be used:
```
Minoa package registry
├─CompanyA
│ ├─ProductA
│ │ ├─Binary
│ │ └─Dylib
│ └─ProductB
│   └─Binary
├─OrgA
│ └─ProductA
│   └─Binary
└─ProductA
  └─Binary
```

In the above example, 3 packages are all named 'ProductA', but they can be distinguished as:
- `CompanyA.ProductA`: Product of a company
- `OrgA.ProductA`: Product of an organization, independent from the company
- `Product`: A seperate product made by a independent developer

## 4.2. Artifacts [↵](#4-package-structure-)

Artifacts, unlike packages, are the direct result of a compilation process or stage.

An artifact consts out of 3 distinct types:
- binaries
- static libraries
- dynamic libraries

Artifact themselves are made up from modules.

### 4.2.1 Binaries [↵](#42-artifacts-)

Binaries are the resulting runnable executables, these are not meant to be 'imported', as they miss all the data required for it.
These can be delivered together with binaries, not only to jjjbe used as the final application, but also tools used for additional functionality.

### 4.2.2. Static ibraries [↵](#42-artifacts-)

A static library is a library that is meant to be linked into any code using it.
It contains all info needed to 'import' and use it in other code, including the bytecode for all the relavent issues.

If possible, the compiler can inline any code within the static library.

### 4.2.3. Dynamic ibraries [↵](#42-artifacts-)

A dynamic library is a library that is meant to be referenced by code linking to it, unlike a static binary, this is not linked directly into the code, but live as their own file right next to it.
Dynamic libraries actually generates 2 resulting file: a Minoa library and a OS-specific dynamic library.
The Minoa library is similar to those produced for static libraries, but does not contain all data that the static library has, i.e. they only include what is needed to successfully build and to reference the dynamic library in the code using it.

## 4.3. Modules [↵](#4-package-structure-)

A module generally represents a single file or inlined module definition (if a file is not directly included within another file).
Each module is allowed to have multiple sub-modules.

### 4.3.1. Main module [↵](#43-modules-)

Each artifact has its own main module, which by default uses the following files:
- binaries: `main.mn`
- static and dynamic libraries: `lib.mn`

These module do not have a namespace from the point of view from the library, as they are essentially code that is located at the root of the library.

### 4.3.2. Module roots [↵](#43-modules-)

A module root is a specially named file which indicates that its sub-modules are located within the same directory as it, instead of in a sub-directory.

A module root is one of the following:
- A binary main module, i.e. `main.mn` when in the binary's root source folder
- A library main module, i.e. `lib.mn` when in a library's root source folder
- `mod.mn` when in a module's sub-folder

# 5. Names and paths [↵](#tables-of-contents)

Names, identifiers, and paths are used to refer to things like:
- types
- items
- generic paramters
- variable bindings
- loop labels
- fields
- attributes
- etc.

## 5.1. Identifiers [↵](#5-names-and-paths-)

```
<iden-name> := <name> | <path-disambig>
<iden>      := <iden-name> [ <generic-args> ]
```

An identifier is a sub-segment of a path, which consists out of a name and optional generic arguments.
Identifiers refer to a single element in a path which can be uniquely identified by its name and generics.

### 5.1.1 Trait disambiguation [↵](#51-identifiers-)

```
<path-disambig> := '(' <trait-path> '.' <name> ')'
```

Sometimes an identifier can not be resolved directly to a trait's item, this can be caused by the item either not being from an `extend` trait impl, or when at least 2 `extend` trait impl items exists that have the same name, but no explicit (non-trait) item exists on the previous item in the path.
This can be resolved by explicitly prepending a path to the implemented trait that has the desired item to be accessed.
The trait path may not end in a function-style trait path end.

## 5.2. Paths [↵](#5-names-and-paths-)

A path is a sequence of one or more identifiers, logically separated by a `.`.
If a path consists out of only one segment, and refers to either an item or variable in the local scope.
If a path has multiple parameters, it refers to an item.

Two examples are:
```
x;
x.y.z;
```

There are multiple types of paths:

### 5.2.1. Simple paths [↵](#53-paths-)

```
<simple-path> := [<simple-path-start>] { '.' <simple-path-segment> }*
<simple-path-start> := '.' | 'super' | 'self'
<simple-path-segment> := <name> | <name>
```

Simple path are used for visitility, attributes, metafunctions and use items.

### 5.2.2. Regular paths [↵](#53-paths-)
```
<path> := [ <path-start> ] <iden> { '.' <iden> }*
```

Paths are used in expression and types to refer to a given item.
They exists out of an optional path start, followed by a sequence of identifiers.

When a path is used in an expression, they are essentially a sequence of field accesses and method calls.
The only part of the path that will be uniquely identified as a path is the path start and the first identifier (without generics).

#### Path start [↵](#532-regular-paths-)

```
<path-start>           := <path-type-start> | <path-self-type-start> | <path-infer-start>
<path-type-start>      := '(' <type> ')' '.'
<path-self-type-start> := 'Self' '.'
<path-infer-start>     := '.' 
```
Path may start with a 1 of 4 different special starts:
- `(type).`: This allows a path to get an associated item out of a type.
- `Self.`: This allows a path to be relative to the current type being implemented. If the type is implementing a trait, the first element will be automatically disambiguated with the trait currently being implemented.
- `.`: This allows the preceeeding path to be inferred from the surrounding context of the path, if the preceeding path cannot be inferred, a compiler error will be emitted.
- `:.`: This allows a path to be directly relative the the current library, ignoring any other modules/scope it is found it, i.e. it looks in the root namespace of the current library.

### 5.2.3. Trait paths [↵](#53-paths-)

```
<trait-path-fn> := <name> '(' <fn-type-params> ')' [ '->' <type-no-bounds> ]
<trait-path> := [ <path-start> ] <type-path-segment> { '.' <type-path-segment> }* [ '.' <trait-path-fn> ]
              |  <trait-path-fn>
```

Trait paths are a special variation of paths that are used in any location a trait is explicitly expected.
A trait path may end in a special function end, the usecase for is limited to the for function call related traits, allowing the parameters and return type for these to be specified.


# 6. Literals [↵](#tables-of-contents)

```
<literal> := <numeric-literals>
           | <boolean-literals>
           | <character-literal>
           | <string-literals>
```

A literal is a compile time constant representing a given value as defined below.

> _Note_: Literals are tokens and will therefore be parsed in the lexer stage_

> _Todo_: Specify how the literal values will be encoded, e.g. decimal values keeping all info so a literal operator can also know that a non-latin decimal character was used

## 6.1. Numeric literals [↵](#6-literals-)

```
<digit_sep> := "_"
<numeric-literals> := <int-decimal-literal>
                    | <float-decimal-literal>
                    | <binary-literal>
                    | <octal-literal>
                    | <int-hexadecimal-literal>
                    | <float-hexadecimal-literal>
```

Numeric literals are literals representing a value of either an integer or floating-point type.

A common feature for integer literals are digit separators.
These don't effect the value represented, but can make the literals more readable to the programmer.
But at most a single digit seperator is allowed to be placed between 2 digits, multiple separators are not allowed, e.g.
Digit seperator also need to between 2 digits, so may not appear before the first digit, or after the last digit, in sequence.

```
1_000 // valid
1___000 // Error: multiple digit separators between 2 digits
_1000 // Error: digit seperator before first digit
1000_ // Error: digit seperator after last digit
```

Numeric literals have no limits on precision and may be seen as infinite precision values.
For this reason, no operations other than converting them to another type are allowed, as there is no known bound on how long such a calculation would take.
And example of this is `1/3`, which results in 0.33 repeating, meaning there is no bound on this calculation.

If a numeric literal is prefixed by either `+` or `-`, these will **not** be part of the literal, but will instead be an operator that is applied to the value.

There are generally 5 categories of numerics literals, and these are defined below.

### 6.1.1. Decimal literal [↵](#61-numeric-literals-)

```
<dec-digit> := ? any unicode character in the category Nd ?
<int-dec-literal> := { <dec-digit> }+
                   | { <dec-digit> }+ ( 'e' | 'E' ) [ '+' ] { dec-digit }+
<float-dec-literal> := { <dec-digit> }+ '.' { [ <digit-serp> ] <dec-digit> }+ [ ( 'e' | 'E' ) [ '-' | '+' ] { dec-digit } { [ <digit-serp> ] <dec-digit> }* ]
                     | { <dec-digit> }+ ( 'e' | 'E' ) [ '-' | '+' ] <hex-digit> { [ <digit-serp> ] <hex-dec-digit> }
```

A decimal literal can represent either an integer or floating point value.
Decimal literals may be prefixed with `0`s without affecting the value, unlike some other languages, this does **not** get interpreted as an octal value and they are ignored.

Decimal literal work with any unicode codepoint representing a decimal digit, including leading 0s.

Floating points have a more complex representation.
They start with at least a single digit, and are then optionally followed by a decimal separator (`.`) and its fractional component.
After this, it is also possible to use scientific notation by writing an 'e' or 'E', followed by the exponent, this will modify the value before it by multiplying it by `10^exponent`.
The exponent is allowed to leave out the optiona `+` for positive exponent values.
The exponent is limited to the range -4932 to 4932 (values outside of this range will be clamped).

If a decimal literal with an exponent does not contain a decimal separator and has a positive exponent, this can also be interpreted as a integer literal.

A decimal separator always needs to be surrounded by a decimal digit, so as not to cause issues while parsing where the `.` could be a field access expression.
As tuple indexing on a decimal literal is not possible, this causes no confusion within the literal.

The exponent indicator `e` needs to be lower case to be consistent with the other indicators within literal values.

The integral and floating point decimal literals are of the type `core:.DecLiteral` and `core:.DecFloatLiteral` repespectively.

#### Examples [↵](#611-decimal-literal-)
```
// Integers
10
195
0042 // value of 42
٤٢ // Arabic-indic 42

// Floating point
0.5
128.64
3e10
005.2 // value of 5.2
۵.٢ // Arabic-indic 5.2
```

### 6.1.2. Binary literals [↵](#61-numeric-literals-)

```
<bin-digit> := '0' | '1'
<bin-literal> := '0b' <bin-digit> { [ <digit-serp> ] <bin-digit> }*
```

A binary literal represents an integer value written as sequence of 0s or 1s, directly representing each bit in the resulting value.

If any character that represents a `<letter>` appears within the literal that is not supported, an error will be generated.

The binary literal indicator uses a lower case `b` for readability, as as uppercase `B` could be confused with `B`.

A binary literal is of type `core:.BinLiteral`.

#### Examples [↵](#612-binary-literals-)
```
0x1010 // decimal value 10
0x1100_0011 // decimal value 195
0x1_1 // decimal value 3
0x1111_1111_1111_1111_1111_1111_1111_1111_1111_1111_1111_1111_1111_1111_1111_1111 // u128::MAX
```

### 6.1.3. Octal literals [↵](#61-numeric-literals-)

```
<oct-digit> := '0'-'7'
<oct-literal> := '0o' <oct-digit> { [ <digit-serp> ] <oct-digit> }*
```

An octal literal represents an integer value written as a sequence of octal values ranging from 0 to 7.

If any character that represents a `<letter>` appears within the literal that is not supported, an error will be generated.

The binary literal indicator uses a lower case `o` for readability, as an uppercase `O` could be confused with `0`.

An octal literal is of type `core:.OctLiteral`

#### Examples [↵](#613-octal-literals-)
```
0o12 // decimal value 10
0x303 // decimal value 195
0x3_0_3 // decimal value 195
0x377_7777_7777_7777_7777_7777_7777_7777_7777_7777_7777 // u128::MAX

```

### 6.1.4. Hexadecimal integer literals [↵](#61-numeric-literals-)

```
<hex-digit> := '0'-'9' | 'a'-'z' | 'A'-'Z'
<int-hex-literal> := '0x' <hex-digit> { [ <digit-serp> ] <hex-digit> }*
```

A hexadecimal literal represents an integer value written as a sequence of nibbles, values ranging from 0 to 9, and then from A/a to F/f.
Mixing lower case and upper case letters is allowed, but is discouraged.
Currently a hexadecimal literal is limited to 32 digits, so not to overflow the maximum value of a 128-bit type.

If any character that represents a `<letter>` appears within the literal that is not supported, an error will be generated.

The binary indicator uses a lower case `x`, although no confusing with an uppercase `X` could occur, this is done to be consistent with both binary an octal indicators.

A hexadecimal literal is of type `core:.HexLiteral`

#### Examples [↵](#614-hexadecimal-integer-literals-)
```
0xA // decimal value 10
0xC3 // decimal value 195
0xC_3 // decimal value 195
0xFFFF_FFFF_FFFF_FFFF_FFFF_FFFF_FFFF_FFFF // u128::MAX

```

### 6.1.5. Hexadecimal floating point literals [↵](#61-numeric-literals-)

```
<float-hex-literal> := '0x' ( '1.' | '0.' ) <hex-digit> { [ <digit-serp> ] <hex-digit> } <float-hex-exponent>
<float-hex-exponent> := 'p' [ '-' | '+' ] '0'-'9' { [ <digit-serp> ] '0'-'9' }*
                      | 'px' [ '-' | '+' ] <hex-digit> { [ <digit-serp> ] <hex-digit> }*
```

In addition to integer hexadecimal literals, there is also support to represent floating points as decimal literals.
These are composed out of a sign, a mantissa and an exponent.

The literal is written with a hexadecimal indicator `0x`. 
This can then be followed by either a `0.` or a `1.`.
After which the exponent indicator `p` appears, followed by an either `-` or `+`, and at the exponent in decimal digits.
Alternatively, if the exponent indicator `px` appears, the exponent is written with hexadecimal digits

When the literal starts with `0x0.`, both the mantissa and exponent are limited to 0.
The special values of 'SNAN', 'QNAN', '-INFINITY' or '+INFINITY' should not be encoded this way, for these values, the associated constant of the type should be used.

If any character that represents a `<letter>` appears within the literal that is not supported, an error will be generated.

The binary indicator uses a lower case `x`, although no confusing with an uppercase `X` could occur, this is done to be consistent with both binary an octal indicators.
The exponent indicator `e` also needs to be lower case for the same reason.

A hexadecimal floating point literal is of type `core:.HexFloatLiteral`

#### Examples [↵](#615-hexadecimal-floating-point-literals-)
```
0x0.0000000000000p0000 // value of 0
+0x0.0000000000000p+0000 // value of 0, but with included signs
-0x0.0000000000000p+0000 // value of -0
0x1.5555555555555p-2 // value of 1/3
0x1.5555_5555_5555_5p-2 // value of 1/3
0x1.0pxF // value of 1e15
```

## 6.2. Boolean literals [↵](#6-literals-)
```
<bool-literal> := 'true' | 'false'
```

A boolean literal represents either a `true` of a `false` value.

A boolean literal is of type `core:.BoolLiteral`

## 6.3. Character literals [↵](#6-literals-)

A character literal defines a character, represented by its unicode codepoints.

A character literal is of type `core:.CharLiteral`

```
<character-literal> := "'" ( ? any unicode codepoint, except for \ and ' ? | <escape-code> ) "'"
```

### 6.3.1. Escaped characters [↵](#tables-of-contents)

```
<escape-code> := '\0'
               | '\t'
               | '\n'
               | '\r'
               | '\"'
               | "\'"
               | '\\'
               | '\p'
               | '\x' <hex-digit> <hex-digit>
               | '\u{' { <hex-digit> }[1,6] '}'
<string-escape-code> := <excape-code>
                      | '\p'
```

An escaped characterf, also known as an escape code, is used to represent certain character values that normally cannot be represented in a character or string.

These can be generally split into 3 categories:
- Simple escape codes
- Hex codes
- Unicode codepoints

A simple escape code exists out of a forward slash `/`, followed by single character.
The following escape codes are available:

code | Escaped codes
-----|---------------------------------------------------------------------------------------
`\0` | U+0000 (NUL)
`\t` | U+0009 (HT)
`\n` | U+000A (LF)
`\r` | U+000D (CR)
`\"` | U+0022 (QUOTATION MARK)
`\'` | U+0027 (APOSTROPHE)
`\\` | U+005C (BACKSLASH)
`\p` | Platform specific linebreaks, i.e. `\r\n` on windows and `\n` on unix-like systems

The special escaped character `\p` is only allowed within string literals, as it may result in a 2 character long sequence.

Hex codes can represent any 8-bit character value using a 2 digit hex code.
It is written as a `\x`, followed by an 2 hex digits.

By default, the hex code is limited to a value within the non-extended ASCII character set, which overlaps with the lowest values in unicode, meaning a value between `x00` and `x7F`.
This behavior can be changed, if a supporting literal operator, or template string expression is used.

Unicode codepoints represent any valid unicode codepoint, including surrogate pairs, this means all characters in the range 0x000000-0x10FFFF.
A unicode escape code is written as `\u{`, followed by between 1 and 6 hex digits, and closed of with a `}`.

#### Examples [↵](#631-escaped-characters-)
```
\n // Newline
\x61 // lower case 'a'
\x81 // Error: hex literal outside of the non-extended ASCII range
\u{1F44D} // '👍', thumb up emoji
```

## 6.4. String literals [↵](#6-literals-)

```
<string-literal>         := <regular-string-literal> | <raw-string-literal>
<regular-string-literal> := '"' { ? any valid unicode codepoint, except for \ and '"' ? | ? string continuation sequence ? | <escape-code> | <string-interpolation> }* '"'
```

A regular string literal is a sequence of any unicode characters, enclosed by two `"` (double quote) characters, with the exception of `"` itself.
A string literal may also include include escaped characters.
They are also limited to being on a single line.

A string literal is of type `core:.StringLiteral`

> _Note_: String literals do not get impacted by normalization, unlike any other text in the source data, as defined in [Normalization](#24-normalization-)

### 6.4.1 Multi-line string literals

```
<multi-line-string-literal>   := { <multi-line-string-segment> }* <string-literal>
<mulit-line=string=segment>   := '"' { ? any valid unicode codepoint, except for \, '"', and <new-line> ? | <escape-code> | <string-interpolation> }* ( <new-line> | <line-continuation-indicator> )
<line-continuation-indicator> := '\' <new-line>
```

A multi-line string is a special variant of a string literal that allows a string to be places accross multiple lines.
To keep to the rule that each line should be able to be independtly parsed without context of any other lines, multi line string are special, in that they exists out of multiple tokens.
Each segments is its own independent token, which start with a `"` (double quote), but ends on a new line, this indicated that the literal continues in the next token.
The multiline literal ends whenever a matching closing `"` is encountered.

As each line is required to start with a `"`, indentation inside of the string can easily be controlled, any preceeding indentation will be ignored, and any succeeding indentation will be part of the the string.

In addition, a single line can to be split up into 2 or more lines by adding a line continuation indicator, meaning that instead of having a newline inserted, the segments act as a single line.
A line continuation indicator is written as a `\`, followed by a new line sequence.

#### Examples [↵](#641-multi-line-string-literals)
```
"This is
"  a multiline
"string"

// Is equivalent to

"This is\n  a muliline\nstring"

// This will also result in the same string, as indentation before the starting `"` is ignored

  "This is
"  a multiline
  "string"
```

An example of a line continuation
```
"This \
"is on a\
" single line"

// Is equivalent to

"This is on a single line"
```

### 6.4.2. Raw string literals
```
<raw-string-literal> := { '#' }[N] '`' { ? any valid unicode codepoint ? }* '`' { '#' }[N]
<raw-multi-line-string-literal> := { <raw-multi-line-string-literal> }* { <raw-string-literal> }
<raw-multi-line-string-literal> := { '#' }[N] '`' { ? any valid unicode codepoint, expect <new-line> ? }* <new-line>
```

Raw string literals are variants of a string literal which does not interpret escaped character specially, instead it interprets them as regular characters in text.
The raw string literal start with a sequence of `#` characters, and then finally a `` ` `` (backtic) and continues until it reaches another `` ` `` that is immediatally followed by sequence with a matching number of `#` is hit.
At minimum 0, and most 256 `#` characters are allowed.

Each segment is its own independent token, which must start with the same amount of `#`, followed by a backtick as the initial line, and ending on a new line, which indicates taht teh literal continues on the next line.
The multiline literal ends whenever a matching closing sequence is encountered.

> _Note_: Any line breaks within raw string literals are part of the resulting string.
> If the exact characters used to form line breaks are semantically relevant to an application,'
> any tools that reanslate line breaks in source code to different formats (between "\n" and "\r\n", for example) will chage application behavior.
> Developers should be carefull in sutch situations

#### Examples [↵](#642-raw-string-literals)
```
`this\n is one\n line`

// Is equivalent to 

"this\\n is one\\n line"


// nesting

#`outer `inner` outer again`#

```

Multi-line raw literals work line:
```
`muli-line
`raw
`string`

#`nested
#` `inner` 
#`multi-line`#

```

### 6.4.3. String interpolation
```
<string-interpolation> := '\' '{' <expr> [ ':' [ 'default' '=' <expr> ',' ] ? format specifiers ? ] '}'
```

A string interpolation allows expressions to be included within a string, allowing it to use the computed value, in addition to a set of format specifiers.
A string interpolation is indicated by `\`, followed by a block, which contains an expression and a format specifier, separated by a `:`.

In addition, when passing an optional type, a default value may be provided to the interpolation, this is done by having a `default` format argument, followed by the default value.A
A comma is then used to separate it from the format arguemnts

The expression used within the interpolation have no requirements by themselves, but these are set by the function or template string that uses them.
In cases where a string with an interpolation does not need special interpolation, this will be converted into just a string representation, as if the `\` was not present.

The format specifier may be any sequence of tokens, and its representation is interpreted by one or more formatters.

# 7. Items [↵](#tables-of-contents)

```
<item> := <module-item>
        | <use-item>
        | <fn-item>
        | <extern-fn-item>
        | <export-fn-item>
        | <type-alias-item>
        | <struct-item>
        | <union-item>
        | <enum-item>
        | <flag-enum-item>
        | <bitfield-item>
        | <const-item>
        | <static-item>
        | <extern-static-item>
        | <trait-item>
        | <impl-item>
        | <external-block>
        | <extension-item>
        | <constraint-item>
        | <meta-item>
```

An item is a component of a package and are organized within it, inside one of its modules.
Every artifact within the package has a single "outermost" anonymous module; all further items within the package have paths within the package hierarchy.

Items are entirely determined at compile time, generally remain fixed during execution, and may reside in read-only memory.

Some items form an implicit scope for the declarations of sub-items.
In other words, within a function or module, declarations of items can (in many cases) be mixed with the statements, control blocks, and similar components that otherwise compose the item body.

## 7.1. Module item [↵](#7-items-)

```
<module-item> := { <attribute>* } [<vis>] [ 'unsafe' ] "mod" <name> ';'
               | { <attribute>* } [<vis>] [ 'unsafe' ] "mod" <name> '{' { <module-attribute> }* { <item> }* '}'
```

A module is a container of zero or more items.

A module item introduces a a new named module into the tree of modules making up the current artifact.
Modules can be nested arbitrarily.

```
mod foo {
    mod bar {
        fn baz() {
            ...
        }
    }

    fn quux() {

    }
}
```

Modules share the same namespace as any other item located within the surrounding item or block.
Declaring a named type with the same name as a module in a scope is forbidden; that is, any item cannot shadow the name of a module in the scope and vice versa.
Items brought into scope with a `use` also have this restriction.

Modules can be marked as `unsafe`, this causes all items within this module that can be marked as `unsafe` to be marked as `unsafe`.

Modules are generally split up in 2 kinds.

### 7.1.1. Inline modules [↵](#71-module-item-)

Inline modules are declared directly within another module and allows manual nesting within the file.

Inline modules are allowed to declare any file modules within them, but the path is interpreted differently.
An inline module also have a single segment path defined to name the sub-folder they would map to if they would have been file modules.

When using a nested module in a file:
```
mod bar {
    mod baz;
}
```
The following set of nested modules will be produces and there  corresponding filesystem structure when using the default module structure:

Module path     | Filesystem path          | File contentd
----------------|--------------------------|----------------
`:`             | `lib.mn` or `main.mn`    | `mod foo;`
`:.foo`         | `foo/mod.mn` or `foo.mn` | see code above
`:.foo.bar`     | `foo/mod.mn` or `foo.mn` | see code above
`:.foo.bar.baz` | `foo/bar/baz.mn`         |

### 7.1.2. File modules [↵](#71-module-item-)

A file module refers to code located within an external file.
If no explicit path is defined for the module, the path to the file will mirror the logical module path.
All ancestor module path elements are represented by a path of nested directories within the artifact's source module.

The default naming of a sub-module is done in the following way:
- When the current file is a [module root](#432-module-roots-), the module file is located within the same directory as the module root's file.
- As a `mod.mn` file within a sub-directory with the module's name.

This is also the same order in which the compiler will scan for the module's file when no `path` attribute is provided

The following is an example of a set of nested modules and there corresponding filesystem structure when using the default module structure:

Module path | Filesystem path                  | File content
------------|----------------------------------|--------------
`:`         | `lib.mn` or `main.mn`            | `mod foo;`
`:.foo`     | `foo/mod.mn` or `foo.mn`         | `mod bar;`
`:.foo.bar` | `foo/bar/mod.mn` or `boo/bar.mn` |

File modules may only appear directly with an artifact's main module or a directly nested module, i.e. only modules nested inside of other modules.

### 7.1.3. Path attribute [↵](#71-module-item-)

The directory and files used for loading a file module can be influenced using the `path` attribute.

If a `path` attribute is applied on a module that is not inside an inline module, the path is relative to the directory the source file is located in.

For example, with the following code in a file:
```
@path("foo.mn")
mod c;
```
will produce the following paths:

Module path    | `c`'s file location | `c`'s module path
---------------|---------------------|-------------------
`src/a/b.mn`   | `src/a/b/foo.mn`    | `:.a.b.c`
`src/a/mod.mn` | `src/a/foo.mn`      | `:.a.c`

For a `path` attribute inside an inline module, the relative location of the file path depends on the kind of source file the `path` attribute is located in.
If in a [module root](#432-module-roots-), the path is relative to the directory the module root is located in.
If it were to only use file modules, meaning that it will interpret all inline module modules as a directories.
Otherwise, it is almost the same, with the exception that the path starts with the name of the current module.

For example, for the following code:
```
mod inline {
    @path("other.mn")
    mod inner;
}
```
The path will be the following depending what file it is in:

Module path    | `inner`'s file location   | `inner`'s module path
---------------|---------------------------|-------------------
`src/a/b.mn`   | `src/a/b/inline/other.mn` | `:.a.b.inline.inner`
`src/a/mod.mn` | `src/a/inline/other.mn`   | `:.a.inline.inner`

The path that would be represented by an inline module may also be defined, as in the following example:
```
@path("foo")
mod inline {
    @path("bar.rs");
    mod inner;
}
```
`inner` would now be located within `foo/bar.rs`.

When a path is applied to an inline module, the path does not require an extension.

## 7.2. Use declarations [↵](#7-items-)

```
<use-item>       := 'use' <use-root> [ ( '.' <use-tree> ) | <use-tree-tail> ] ';'
                  | <use-tree> ';'
<use-root>       := [ <package-name> ] ':' [ <module-name> ]
                  | 'super'
<package-name>   := [ <name> '.' ] <name>
<module-name>    := <name>
<use-tree-tail>  := <use-glob>
                  | <use-grouping>
                  | <use-alias>
<use-tree>       := <use-path> <use-tree-tail>
<use-tree-inner> := <use-tree>
                  | 'self' [ 'as' <name> ]
<use-path>       := <name> { '.' <name> }*
```

A `use` declaration creates a local binding associated to a item path.
They can be used to:
- introduce a library's root module into the scope
- introduce a specific item into the scope
- Shorten the path required to reference an item

These declarations may appear in modules and blocks.

> _Note_: To import the contents of a file, use the `#embed()` meta-function

> _Todo_: Could be interesting to support directly including C headers, i.e. `use "header.h";` and `use "header.h" as ffi;`

### 7.2.1. Use visibility [↵](#72-use-declarations-)

Like other items, `use` declarations are private to the containing module by default.
But it can also have its visibility declared, while for most items, this is explained in the [visibility sectoin](#16-visibility-), visibility attributes work slightly differently on `use` declarations.
`use` declaration can be used to re-export symbols to a different target definition with a different visibility and/or name.
For example, a symbol with a more restricted visibility like 'private' in one module, can be changed to a `pub` symbol in another module.
If the resulting sequence of re-exports form a cycle or cannot be resolved, a compiler error will be emitted.

An example of redirection:
```
mod quux {
    use :.foo.{bar, baz};
    pub mod foo {
        pub fn bar() {}
        pub fn baz() {}
    }
}

fn main {
    quux.bar();
    quux.baz();
}
```

### 7.2.2. Use root [↵](#72-use-declarations-)

Each use path starts with a root which indicates relative to what the path is located.

A use declaration may look for the path in within one of the following:
- a specified library
- the current module
- the parent module

To access a a specified library, the declaration start by indicating the path to the library.
This is called the root name and is shown as `package:library`, these do not need to explicitly be written down in the following usecases:
- the package can be left out if the path refers to the current package
- the library can be left out in 2 cases:
    - If there is no explicit package (i.e. the current package), it will refer to the current library or binary
    - If there is an explicit package, it will refer to the library within that package with that same name

An example of this can be seen in the below table for the following project structure:
```
A (package)
- Cur (lib)
- A (lib)
- C (lib)
B (package)
- B (lib)
- D (lib)
```

And with the current library being `Cur`, the path will point to the following packages and libraries

Use root | Package | Library
---------|---------|---------
`:`      | `A`     | `Cur`
`:C`     | `A`     | `C`
`A:`     | `A`     | `A`
`B:`     | `B`     | `B`
`B:D`    | `B`     | `D`

The package name may contain 2 parts: an optional group name, and the actual name of the package.
For more info can be found [here](#41-packages-).

For example, the Minoa std packages' experimental library is accessed as `Minoa.std:experimental`, where:
- `Minoa` is the group that the library belongs to
- `std` is the package under which the library is distributed
- `experimental` is the actual library.

The `use` root can be omitted for any value relative to the current module, including at most 1 level up using the `super` keyword.

### 7.2.3. Use path [↵](#72-use-declarations-)

A use path is the main path to a symbol which appears after after a root, or as the first element for a sub-path within a [use grouping](#724-use-grouping-).
A use path exists out of a list of names, which can each refer to a entity within the previous segments namespace.

Items each segments may refer to can be one of the following:
- Nameable [items](#7-items-)
- [Enum variants](#11119-enum-types-)
- Builtin types ([primitive types](#1114-primitive-types-) & [string slices](#11111-string-slice-types-))
- [Attributes](#17-attributes-)
- [Metaprogramming items](#13-metaprogramming-)

The cannot refer to any of the following:
- associated items
- generic parameters
- local variables
- tool attributes
- items declared inside of [functions](#73-function-)

If no path tail is added, the path will create a binding to the imported entities, with the exception of [`self`](#self-imports-).

A use-path may end in a tail, which is one of the following:
- [glob imports](#723-glob-imports-)
- [use groupings](#724-use-groupings-)
- [use aliases](#725-use-aliases-)

### 7.2.4. Glob imports [↵](#72-use-declarations-)
```
<use-glob> := '.' '*'
```

A glob import is used to import all entities that are located in the namespace pointed to by the preceeding path.

Items and named imports are allowed to shadow names from glob impoorts within the same namespace.
Meaning that if a name is already defined, while also being imported via a glob, the name that is already defined takes precedences over the glob imported entity.

### 7.2.5. Use groupings [↵](#72-use-declarations-)
```
<use-grouping>   := '.' '{' <use-tree-inner> { ',' <use-tree-inner> }* [ ',' ] '}'
<use-tree-inner> := <use-tree>
                  | 'self' [ <use-alias> ]
```

A use grouping allows multiple entities to be imported from the location pointed to by the preceeding path.
Use grouping can be nested within each other.

An emty use-grouping is allowed, but this will not import anything, although the preceeding path will still be checked to be valid.

#### `self` imports [↵](#724-use-grouping-)

A `self` import is a special import that is only allowed inside of an import grouping.
It allows the entity at the sub-path before the grouping to be imported in addition to any other entities in the path.

If the `self` import is the only element in an import grouping, the path will act the same as it would have been without the use grouping.

### 7.2.6. Use aliases [↵](#72-use-declarations-)
```
<use-alias> := 'as' <name>
```

A use alias allows an imported entity to be bound to another name.

#### Underscore imports [↵](#725-use-aliases-)

Items can be imported without binding to a name by using an `_` (underscore) alias.
This is particularly useful to import a trait so that its methods may be used without importing the trait symbol, for example if the trait's symbol may conflict with another symbol.

### 7.2.6. Import ambiguity [↵](#72-use-declarations-)

Certain situation might cause multiple `use`s to result in an ambiguity when trying to resolve a symbol, and the symbols do not resolve to the same entity.

Glob imports are allowed to import conflicting names, as long as the name is not used.
If the the conflicting names were to refer to the same entity, the name may still be used.
The visibility of such an entity will then become the greatest visibility between the imports.

For example, if both libraries were to contain `foo`, the following would lead to an ambiguity:
```
use :lib0.*;
use :lib1.*;

fn main() {
    foo(); // ambiguous symbol, specify `lib0.foo` or `lib1.foo`
}
```
it is then possible to explicitly specify which lib `foo` should come from
```
use :lib0.*;
use :lib1.{*, foo}; // Still importing everything, but foo is named now

fn main() {
    foo(); // This is now `lib1.foo`
}
```

## 7.3 Function [↵](#7-items-)
```
<fn-item>       := <fn-qualifiers> 'fn' <name> [ <deduced-params> ] '(' [ <fn-params> ] ')' [ <fn-return> ] [ <where-clause> ] { <contract> }* <block>
<fn-qualifiers> := { <attribute>* } [ <vis> ] [ 'const' | 'async' | 'lazy' ] [ 'unsafe' ]
```

A function consists of a named block of code, together with a set of parameters (implicit and explicit), and a return type, representing a unit of reusable code.

When refered to, a function yields the corresponding zero-sized [function type](#11121-function-types-), when when called evaluates to a direct function call.

A function may be declared `unsafe`, making it's entire body an unsafe context, but requires the function itself to be called from an unsafe context.

Each function has an identifier used to look up the function.
A function identifier is based on the actual function name + the names of the labels of the required and optional parameters.
This is done by having taking the function name, and following that with a list of `:`-separated label names, surrounded by parentheses.
These is always a trailing `:`.
Optional paramters are prefixed with a `?`.
If a variadic paramter exists, the label will be followed by `...`
For example, the function `foo(a: i32, b: i32 = 1, c...: i32)` will have the identifier `foo(a:?b:...)`.

Some examples of this:
```
fn foo() {} // -> foo()
fn foo(a: i32, b: i32) {} // -> foo(a:b:)
fn foo(:_ a: i32, :_ b: i32) {} // -> foo(_:b:)
fn foo(a: i32, :_ b: i32, :_ c: i32, d: i32) {} // -> foo(a:_:_:b:)

// With optionals
fn foo(a: i32 = 2) {} // -> foo(?a:)
fn foo(a: i32, b: i32 = 3) {} // -> foo(a:?b:)
```
> _Todo_: Effects

### 7.3.1. Parameters [↵](#73-function-)
```
<fn-params>      := <fn-param> { ',' <fn-param> }* [ ',' ]
                  | [ <fn-req-params> ',' ] <fn-opt-params> [ ',' ]
                  | [ <fn-req-params> ',' ] [ <fn-opt-params> ',' ] <fn-variadic-params>

<fn-req-params>  := <fn-param> { ',' <fn-param> }*
<fn-param>       := { <attribute> }* <fn-param-name> ':' <type>

<fn-param-name>  := [ <fn-param-label> ] [ 'mut' | 'const' ] <name> { ',' [ <fn-param-label> ] [ 'mut' | 'const' ] <name> }
                  | [ <fn-param-label> ] [ 'const' ] <pattern-top-no-alt>
```

A function parameter represents one or more values that can be passed to a function.
Each parameter can be specified by either
- a collection of one of more comma-separated names, each with a mutability specified, or
- a pattern

If multiple names are provided, the parameter will be split into multiple invidual paramters.
Otherwise, if a  pattern is used within a parameter, it must be an [irrifutable pattern](), for example:
```
fn name((value, _): (i32, i32)) -> i32 { value }
```

Parameter may be passed either as:
- `mut`: meaning that the value passed via the parameter may change, or
- `const`: meaning that the parameter is a compile-time parameter.

If a pattern is used, the user may apply a `const` modifier to the entire pattern.
The programmer must then ensure that no mutable bindings appear withing the pattern.

Any parameter of type `type` must be a constant parameter.

Any 2 parameters may not share a label.

> _Todo_: Fix irrifutable link

> _Note_: In addition to strong keywords, the pattern used for a parameter may not include the weak keyword `lazy`

#### Labels [↵](#731-parameters-)
```
<fn-param-label> := <name>
```
Labels provide an explicit name to a given argument, which is then used when calling the function.
These calls require labels to be provided to be able to select the correct version of a function

If an explicit label is provided, it can either be:
- a name, this will then be the name will be the label bound to the given parameter and must be used to pass data along the the function, or
- an `_`, this implies a label-less parameter, meaning that no name needs to be provided when calling a function

If no explicit label is provided, a label is picked depending on the parameter:
- for every name, the label will be called the same, e.g. `foo: i32` will become `foo foo : i32`
- for every pattern, the parameter will be a label-less parameter.

Argument labels are also allowed to take on the name of keywords, with the exception of the `self` keywords, as it is a special label.
For example: `fn find(struct value: Foo, in container: Bar)`.

> _Note_: A label of `self` is not allowed

> _Note_: Labels don't need an explicit syntax to be introduced, as 2 identifier following each other can never be a pattern, so it will always be a label & pattern

> _Todo_: Should labels be allowed to be keywords? as label are clearly marked

#### Optional parameters [↵](#731-parameters-)
```
<fn-opt-params> := <fn-opt-param> { ',' <fn-opt-param> }
<fn-opt-param>  := <fn-param> '=' <expr>
```

A parameter may be provided with a default value for when no explicit value is given when calling the function, these are known as optional parameters.
Optional parameters must appear after all non-optional (also known as required) parameters.
A default value needs to a be a value that is known at compile-time.

When calling a function with optional parameters, each argument may appear in any order relative to other optional arguments, but they must appear after any required arguments.

An optional parameter may not be label-less, i.e. it may not have `:_` as a label.

#### Variadic parameters [↵](#731-parameters-)
```
<variadic-param>  := <var-param-names> '...' ':' <var-param-types> [ '=' <expr> { ',' <expr> } [ ',' ] ]
<var-param-names> := <name> { ',' <name> }*
<var-param-types> := <type> { ',' <type> }*
```

A variadic parameter allows for 0 or more instances of its parameter set to be passed to a function.
They therefore allow for an unknown amount of parameters to be passed to a function/method.

A parameter set is the collection of names/types provided to the variadic parameter.
Therefore, the number of names and types provided must match.

When providing values, for parameter set, all elements need to be passed, meaning that the total amount of arguments passed as variadic arguments needs to be an integer multiple of the number of names defined within the variadic parameter's definition.

If a default value is provided an no values are provided at the call site, the variadic parameter will have the default value.

#### Deduced parameters [↵](#731-parameters-)
```
<deduced-params> := '[' <deduced-param> { ',' <deduced-param> } ']'
<deduced-param>  := <name> ':' <type>
```

A deduced parameter is a parameter that can be deduces from the arguments passed into the function.
All deduced parameters are automatically compile-time values.

Example
```
// Both `T` and `N` can be inferred from the type of `arr`
fn foo[T: type, N: usize](arr: [N]T) { ... }
```

If a deduced parameter has not enough information from the rest of the function, an compiler error will be emitted.
For example: 
```
fn foo[T: type, U: type](val: &T) -> U { ... } // error: cannot decude the type of `U` from the signature
```

#### Lazy parameters

Lazy parameters allow for late execution of a parameters, or even avoid execution when this parameter is not utilized.
But unlike closure arguments, lazy parameters allow the compiler to lift additional code in the surrounding context into the closure for the lazy variable.
This is limited to any code which only the lazy argument is relying on.

> _Note_: These are essentially syntactic sugar for closures taking in [autoclosure](#9165-autoclosures-) arguments

> _Note_: Lazy parameters do introduce a deduced parameter into the function for the closure.

### 7.3.2. Return [↵](#73-function-)
```
<fn-return> := '->' [ <name> ':' ] <type>
```
A function return may define a return in 2 ways:
- using a named return, allowing the name to be assigned within the function, this is the value that will be returned when no explicit return is called.
  Meaning that this may still be overwritten explicitly.
- using an unnamed type, meaning that a value must be expliclitly returned from a function.

A special case is when the return type is a named tuple with all fields named. In this case, the individual fields may be assigned directly like a names return would.

If a named return exists and no explicit return is used, the named return or the named tuple fields must be assigned a value.

When no return is explicitly stated, an implicit [unit type](#1115-unit-type-) will be the return type.

### 7.3.3. Function body [↵](#731-parameters-)

A function body is the part of the function that contain the actual code that is run when calling the function.
Within a function body, the programmer has access to all parameters and named return values.

When a parameter uses a pattern, the body has access the the bindings within the pattern.

These act like a [block expression](#95-block-expression--), allowing both explicit returns, and returns using the last expression within the block.

### 7.3.4. Const functions [↵](#73-function-)

A const function is a version of a function which may be called either at compile time or at runtime.

Functions returning a [`type` type](#1111-type-type-) are restricted to a compile time context.

### 7.3.5. Methods [↵](#73-function-)
```
<method>             := <method-qualifiers> 'fn' '(' <receiver> ')' <name> [ <implicit-params> ] '(' [ <fn-params> ] ')' [ <fn-return> ] [ <where-clause> ] { <contract> }* <block>
<method-qualifiers>  := { <attribute> }* [ 'const' ] [ 'unsafe' ]
<reciever>           := [ '&' ] [ 'mut' ] 'self'
                      | 'self' ':' <type>
```

A method is a special type of associated function which also takes in a receiver, allows a function to be called directly on a value of a given type.
Methocs need to be associated with a type, there are also special versions of a method for traits and constraints.

A methods receiver is located seperately from the parameters, as it is a special inferred parameter, which works differently than other inferred parameters.
The receiver, unlike inferred parameters is not a compile-time parameter, but a runtime parameter. 
It also helps as a visual aid to quickly distinguish methods.

A receiver may have one of the following types:
- `Self`
- `&Self`
- `&mut Self`
- any `T` that implements `Deref(.Target = Self)`

> _Todo_: `T` might need some kind of `Dispatch` trait associated with it instead of `Deref`

The receiver may also be written in shorthand, which results in the following full receivers:
shorthand  | full
-----------|------------------
`self`     | `self: Self`
`&self`    | `self: &Self`
`&mut self`| `self: &mut Self`

When the `self` shorthand is used, `mut` may be added to make the value mutable.

When a method is called as if it were a function, the receiver can be passed as a parameter with the `self` label.

### 7.3.6. Trait functions & methods [↵](#73-function-)
```
<trait-fn>            := <trait-fn-qualifiers> 'fn' <name> [ '?' ] [ <deduced-params> ] '(' [ <fn-params> ] ')' [ <fn-return> ] [ <where-clause> ] { <contract> }* <trait-fn-body>
<trait-method>        := <trait-fn-qualifiers> 'fn' '(' <receiver> ')' <name> [ '?' ] [ <deduced-params> ] '(' [ <fn-params> ] ')' [ <fn-return> ] [ <where-clause> ] { <contract> }* <trait-fn-body>
<trait-fn-qualifiers> := { <attribute> }* [ 'const' | 'async' ] [ 'unsafe' ]
<trait-fn-body>       := ';' | <block>
```

Trait functions and methods are similar to their normal counterparts, but define a signature which a function or methods in a trait impl needs to implement.

They can be provided with a default implementation, if one is provided, they are not required to be implemeneted within their trait impl, but may be overriden with a specialized implementation.

If a trait function or method has a set of contracts, any implementation needs to adhere to the given contracts.

In addition, trait support so-called optional functions/methods, these are defined by having a `?` following the name of the function.
This does not require the function/method to have an implementation on the implementing type, allowing it to only be supplied when neccesary for any use to correctly function.
These functions/methods need to be called using the 

### 7.3.7. External & exported functions [↵](#73-function-)
```
<extern-fn-item>       := { <attribute> } [ 'safe' ] 'extern' <string-literal> 'fn' <name> '(' <extern-fn-params> ')' [ '->' <type> ] ';'
<extern-block-fn-item> := { <attribute> } [ 'safe' ] 'fn' <name> '(' <extern-fn-params> ')' [ '->' <type> ] ';'

<export-fn-item>       := { <attribute> } 'export' fn <name> '(' <extern-fn-params> ')' [ '->' <type> ] <block>
<export-block-fn-item> := { <attribute> } 'fn' <name> '(' <extern-fn-params> ')' [ '->' <type> ] <block>
```
d
Functions can be declared as `extern` or `export`, allowing interaction with external code.
If a function is `extern`, it cannot have a body, meanwhile `export` functions must have a body.

`extern` functions are declared with the name of the external library the symbol is found it.
The compiler will automatically add a prefix and an extension to these names.
The prefix and extension will depend on the OS being compiled for.
Individual function may be explicitly marked as safe, if the safety can be guaranteed by the implementer

Both `extern` and `export` function will default to a `.C` calling convention.
The blocks may define the default calling convention using the [`@callconv` attribute](#callconv).

> _Note_: For more information, check the [external & export blocks](#714-external--export-blocks-) section.

> _Note_: To set a non-external function to use the `contextless` ABI, use the [`contextless`](#contextless) attribute.

### 7.3.8. Generator functions

A generator function is a function that contains one or more [`yield`](#934-yield-expresssions-)s.
It is allows for writing iterators as if they were normal functions, but can return multiple values.

A generator function must return an `impl Iterator` type.

A generator function will continue to produce values until its last `yield`, or a return is encountered.

### 7.3.9. Async functions [↵](#73-function-)

An async function allows a function to be called in an async context.

They do not work like normal functions where they are just executed when called.
Instead they capture their arguments into a future, which can then be polled to execute the actual contents of the function.

An async function is syntactic sugar for a function with a very specific signature.
This is roughly equivalent to a function returning a `Future`, nad having an `async` block in it's body, i.e.
```
async fn example(x: &str) -> usize {
    x.len()
}
```
is equivalent to
```
fn example(x: &str) -> impl Future(.Output = usize) {
    async move {
        x.len()
    }
}
```

Although in practice, this is more complex:
- the return type needs to keep track of each argument's lifetime
- the `async` block captures all parameters, even unused ones, to ensure the correct drop order, as it would be a regular function.

> _Todo_: Async functions should be able to be called from outside of an async context, avoiding the need for colored function. The exact details are not yet figured out

#### Async generator functions [↵](#73-async-functions-)

An async generator funcition, as its name implies, is a generator function declared as async, allowing it to use everything an async function can use.
Each yi

#### Async unsafe functions [↵](#739-async-functions-)

An async function may also be declared to be unsafe.
The resulting function can therefore do whatever an unsafe function is allowed to.

While calling the function is unsafe, the future returned by the async function is not, and can therefore be awaited outside of an unsafe context.

### 7.3.10. Label-based overloading [↵](#73-function-)

Support for function overloading is based on labels.
Since label names are required to be used, we can check collisions when processing the functions themselves and we don't need to worry about them when trying to resolve the symbol.

First, each function will be converted to a collection of possible variants which are checked.
Variants are produces by going over every possible combination of optional parameters, and creating an function identifier from them.
For example, `foo(a: i32 = 0, b: i32 = 1)` would have the following variants to be checked:
- `foo()`
- `foo(a)`
- `foo(b)`
- `foo(a:b)`

Then each variant will be compared to the variants of the function it is compared to.
If a collision exists, a compiler error will be emitted.

> _Note_: Since optional arguments may appear in any order, the function `foo(a: i32 = 0, b: i32 = 0)` will check for both if either the identifier `foo(?a:?b)` or `foo(?b:?a)` would collide

#### Examples [↵](#7310-label-based-overloading-)

1. Different names: **_no collision_**
```
fn foo() { ... } // foo()
fn bar() { ... } // bar()
```

2. Same number of paramters with same labels: **_collision_**
```
fn foo() { ... } // foo()
fn foo() { ... } // foo()
```
or
```
fn foo(a: i32) { ... } // foo(a:)
fn foo(a: f64) { ... } // foo(a:)
```

3. Overlap between func with required and func with default values: **_collision_**

(0) collides with (1), since (1) has the variant `foo(a:b)`
```
fn foo(a: i32, b: i32)     // (0) foo(a:b:)
fn foo(a: i32, b: i32 = 0) // (1) foo(a:?b:)
```
or (0) collides with (1), since (0) and (1) both have `foo(a)` and `foo(a:c)` as shared variants
```
fn foo(a: i32,             c: i32)     // (0) foo(a:?c:)
fn foo(a: f64, b: i32 = 0, c: i32 = 1) // (1) foo(a:?b:?c:)
```

4. Overlap between func with required and func with default values, but with a non-default left over: **_no collision_**

In both examples, (0) always needs to have `d` passed, but (1) has no variant with `d`

```
fn foo(a: i32, b: i32, d: i32) // (0) foo(a:b:d:)
fn foo(a: i32, b: i32 = 0)     // (1) foo(a:?b:)
```
or
```
fn foo(a: i32,             c: i32, d: i32) // (1) foo(a:c:d:)
fn foo(a: f64, b: i32 = 0, c: i32 = 1)     // (1) foo(a:?b:?c:)
```

5. Any left over defaults: **_collision_**

(0) collides with (1), since (1) has the variant `foo()`
```
fn foo()           // (0) foo()
fn foo(a: i32 = 0) // (1) foo(?a:)
```
or (0) collides with (1), since (0) and (1) both have `foo(a)` and `foo(a:c)` as shared variants
```
fn foo(a: i32, c: i32 = 0) // (0) foo(a:?c:)
fn foo(a: i32, c: i32 = 1) // (1) foo(a:?c:)
```

6. Both have variadics: **_collision_**

> _Todo_: Correct syntax + figure out new variadics

```
fn foo(a: i32, b: i32...) // foo(a:...)
fn foo(a: i32, c: f64...) // foo(a:...)
```
or 
```
fn foo(a: i32,             c: i32...) // foo(a:...)
fn foo(a: i32, b: i32 = 1, c: i32...) // foo(a:?b:...)
```

### 7.3.11. String-template functions [↵](#73-function-)
```
<string-template-fn> := <fn-qualifiers> 'template' 'fn' <name> [ <deduced-params> ] '(' <name> ':' ? Interpolated stringtype ? [ ',' <variadic-params> ] ')' [ '->` <type> ]  [ <where-clause> ] { <contract> }* <block>
```

A string-template function is a special function which can be used by a [template string expression](#931-template-string-expression).

It takes in an interpolated string, and optionally a set of variadic arguments, which can be used to pass values without interpolating them within the string itself.
A common usecase of this is to map them to a `{N}` occurance within the string, and insert them there.

## 7.4 Type aliases [↵](#7-items-)

```
<type-alias-item> := { <attribute*> } [ <vis> ] ( <type-alias> | <new-type> | <adapt-type> )
<type-alias>      := 'type' <name> [ <generic-params> ] '=' <type> [ <where-clause> ] ';'
<new-type>        := 'distinct' 'type' <name> [ <generic-params> ] '=' <type> <new-type-body>
<adapt-type>      := 'adapt' 'type' <name> [ <generic-params> ] '=' <type> <new-type-body>
<new-type-body>   := ';'
                   | '{' <assoc-items>* '}'
```

A type alias defines a new name for an existing type, and allows for partial specialization of the generic parameters.
The 'alias type' is the new type being created, the 'aliasee' is the type that is being aliased, i.e. `type alias_type = aliasee;`.

If a generic type is passed to the aliasee, the generic in the alias type itself will gain the same bounds as those for the aliasee.

There are 3 'variants' of the type alias.

### 7.4.1. Distinct type aliases [↵](#74-type-aliases-)

A distinct type is a special type alias, that does not only gives a different name, etc to a type, but splits it off into a separate type, these are also known as 'newtypes.'
As the name implies, they are distinct, and cannot be cast to from the alisee.

Distinct types take over all fields and functionality of the aliasee, but can also implement additional functionality independently of the aliasee's type.

> _Note_: a limitation of this is that a disctinct type cannot acces fields that are private to the aliasee.

### 7.4.2. Adapt type aliases [↵](#74-type-aliases-)

An adapt type alias is similar to a distinct type, but allows the aliasee to be cast to the adapt type.

This can be used to add different interfaces to a given type, to allow a different context when they are used.

For example:
```
struct Song { ... }

adapt type SongByArtist = Song {
    impl as Cmp { ... }
}

adapt type SongByArtist = Song {
    impl as Cmp { ... }
}
```
Using this, the behavior of the type `Song` can be changed depending on which adapt type it is cast to.
An example of it's use is:
```
let songs = ...;

sort_by_key(&mut songs, fn(song) => song as SongByArtist);
sort_by_key(&mut songs, fn(song) => song as SongByTitle);
```

> _Todo:_ Figure out correct closure syntax + sort API

### 7.4.3. Trait type aliases [↵](#74-type-aliases-)
```
<trait-type-alias> := 'type' <name> [ <generic-params> ] [ ':' <generic-type-bounds> ] [ <where-clause> ] [ '=' <type> ] ';'
```

A trait type alias definition declared a signature for an impl type alias implementation.
They are similar to normal type aliases, but can be overwritten by an implementation.

In addition, a trait bound can also be declared on the type alias.
When a trait bound is defined, it requires any type which can be used as the associated type to implement those traits.
An implicit `Sized` trait is applied on the type alias, but can be relaxed using the `?Sized` bound.

A default type can be provided which will be used when no explicit type alias is defined within an implementation.

## 7.5. Structs [↵](#7-items-)

```
<struct-item> := { <attribute> }* [ <vis> ] ( <struct> | <tuple-struct> | <unit-struct> )
```

A structure item is syntactic sugar to be able to more easily define a structure type.
This can either be a structure or tuple structure type, depending on the syntax.

A structure's visibility defines only the visibility of the structure, and not any of its fields.

Meanwhile, a structure's mutability will be propagated as the mutability of the structure.

### 7.5.1. Regular structs [↵](#75-structs-)
```
<struct> := [ 'mut' | 'record' ] 'struct' [ <generic-params> ] [ <where-clause> ] '{' { <struct-elements> } '}'
```

A struct item generates a named [struct type](#11116-struct-types-).
Its contents are declared in the same way as a struct type.

The following struct declaration:
```
struct Foo {
    // ...
}

struct Bar(T: type, N: usize) {
    // ...
}
```
Is equal to the following:
```
type Foo = struct {
    // ...
};

type Bar = struct[T: type, N: usize] {
    // ...
};
```

### 7.5.2. Tuple structure [↵](#75-structs-)
```
<tuple-struct>      := [ 'mut' | 'record' ] 'struct' [ <generic-params> ] [ <where-clause> ] '(' <tuple-struct-fields> ')' <tuple-struct-body>
<tuple-struct-body> := ';'
                     | '{' { <assoc-item> }* '}'
```

A tuple struct item generates a named [tuple struct type](#11117-tuple-struct-types-).
Its content are declared the same way as a tuple struct type.

A tuple struct declaration without any generic arguments, like:
```
struct name(...);

struct name2(...) { ... }
```
Is equal to the following:
```
type name = struct (...);
type name2 = struct ( ... ) { ... };
```

> _Todo_: Generics

### 7.5.3. Unit structs [↵](#75-structs-)

```
<unit-struct>           := { <attribute> }* [ <vis> ] 'struct' <name> <unit-struct-decl-body>
<unit-struct-decl-body> := ';'
                         | '{' { <struct-element> }* '}'
```

A unit struct item declares a named [unit struct type](#unit-structs-).
Its declaration, like its related type, is identical to a regular struct, except that is contains no fields.

A unit struct declaration:
```
struct Unit;
struct Unit2 { ... }
```
Is equal to the following
```
type Unit = struct;
type Unit2 = struct { ... };
```

## 7.6. Union [↵](#7-items-)
```
<union-item> := { <attribute> }* [ <vis> ] [ 'mut' ] 'union' <name>  [ <generic-params> ] [ <where-clause> ] '{' <union-members> '}'
```
A union item is syntactic sugar to more easily define a named [union type](#11118-union-types-).

A union's visibility defines only the visibility of the union, and not any of its fields.

Meanwhile, an enum's mutability will be propagated as the mutability of the union.

The following struct declaration:
```
union Foo {
    // ...
}

union Bar(T: type, N: usize) {
    // ...
}
```
Is equal to the following:
```
type Foo = union {
    // ...
};

type Bar = union[T: type, N: usize] {
    // ...
};
```

## 7.7. Enum [↵](#7-items-)

```
<enum-item>   := { <attribute> }* [ <vis> ] ( <adt-enum> | <record-enum> | <flag-enum> )
<adt-enum>    := [ 'mut' ] 'enum' <name> [ <generic-params> ] [ <where-clause> ] '{' <enum-members> '}'
<record-enum> := 'record' 'enum' <name> [ <generic-params> ] [ <where-clause> ] '{' <record-enum-members> '}'
```

An enum items is syntactic sugar to more easily define a named [enum type](#11119-enum-types-).

An enum's visibility defines only the visibility of the enum, and not any of its fields.

Meanwhile, an enum's mutability will be propagated as the mutability of the enum.

An enum declaration without any generic arguments, like:
```
enum name {
    // ...
}
```
Is equal to the following:
```
type name = enum {
    // ...
};
```

> _Todo_: Generics


### 7.7.1. Flag enum [↵](#77-enum-)
```
<flag-enum-item> := 'flag' 'enum' <name> '{' <flag-enum-members> '}'
```

A flag enum item is similar to the one above, and declares a [flag enum type](#flag-enum-types-).

A flag enum meanwhile cannot be declared with generics
```
flag enum {
    // ...
}
```
Is equal to the following
```
type name = flag enum {
    // ...
}
```

## 7.8. Bitfield [↵](#7-items-)
```
<bitfield-item> := { <attribute> }*  [ <vis> ] [ 'record' | 'mut' ] 'bitfield' <name> [ <generic-params> ] [ ':' <expr> ] [ <where-clause> ] '{' <bitfield-members> '}'
```

A bitfield item is syntactic sugar to more easily define a named [bitfield type](#11120-bitfield-types-).

A bitfield's visibility defines only the visibility of the bitfield, and not any of its fields.

Meanwhile, a bitfield's mutability will be propagated as the mutability of the bitfield.

An enum declaration without any generic arguments, like:
```
bitfield name {
    // ...
}
```
Is equal to the following:
```
type name = bitfield {
    // ...
};
```

> _Todo_: Generics

## 7.9. Const item [↵](#7-items-)

```
<const-item> := { <attribute> }* [ <vis> ] 'const' <name> [ ':' <type> ] '=' <expr> ';'
```

A constant item is a named constant value which is not associated with a specific memory location, i.e. the value is known at compile time.
Contants are essentially inlined when they are used, meaning that they are copied directly into the relevant context when used.
This includes constants from other libraries and non-`Copy` items.

When a reference is taken to a constant value from different locations, they are not neccesarily guarenteed to point to the same memory location.

Constants are generally explicitly types, unless a certain sub-set expressions are used, these are:
- literal expression with a literal operator
- _TODO: others_

Constants live throught the entirety of the program and any reference to them is always valid.

Constants may be of types that have a destructor, and will be dropped when the copy of the value that they are assigned to go out of scope.

When defined inside of an implementation, the const item will be an associated with that type.

### 7.9.1. trait constant [↵](#79-const-item-)

```
<trait-const> := 'const' <name> ':' <type> [ '=' <expr> ] ';'
```

An trait constant declares a signature for an associated constant implementation, i.e. it declares both the name and the type the associated constant should have.

A default value can be provided which will be used when no explicit constant is defined within an implementation.


## 7.10. Static item [↵](#7-items-)

```
<static-item> := { <attribute> }* [ <vis> ] [ [ 'mut' ] 'tls' ] 'static' <name> [ ':' <type> ] '=' <expr> ';'
<extern-static-item> := { <attribute> }* [ <vis> ] [ 'mut' ] [ 'tls' ] 'static' <name> [ ':' <type> ] ';'
```

A static item is a named location within the program's static memory.
All references to a static refer to the same memory location.

Static items live for the entirety of the programs life and are never dropped at the end of the program.
If a static has a droppable value, it is up to the programmer to make sure the data is dropped at the end of the program.

Any 0-sized static variable will take up at minimum 1-byte.

Static items must be initialized using an expression that can be evaluated at compile time.

Non-mutable static items that do not support interior mutability and will be allocated in read-only static memory.

All access to statics is safe, but there are a number of restrictions:
- The type must have a `Sync` trait bound to allow thread-safe access.
- Statics may not be refered to from a constant.

### 7.10.1. Thread local storage [↵](#710-static-item-)

Static values may also be allocated as thread local storage, using the weak `tls` keyword before the `static` keyword.
Tls statics are unique to the thread they are running on and are not shared with other threads.

Unlike static items, a thread local static can be mutable without requiring [interior mutability](#115-interior-mutability-), as it can only be accessed from the current thread.

### 7.10.3. Statics and generics [↵](#710-static-item-)

When a static variable is declared within a generic scope, it will result in exactly 1 static item being defined, shared accross all monomorphization of that scope.

### 7.10.3 External statics [↵](#710-static-item-)

```
<extern-static>       := { <attribute> }* [ <vis> ] 'extern' <string-literal> ['mut']  <name> ':' <type> ';'
<extern-block-static> := { <attribute> }* [ <vis> ] ['mut']  <name> ':' <type> ';'

<export-static>       := { <attribute> }* [ <vis> ] 'export' ['mut']  <name> ':' <type> '=' <expr> ';'
<export-block-static> := { <attribute> }* [ <vis> ] ['mut']  <name> ':' <type> '=' <expr> ';'
```

Statics can be declared as `extern` or `export`, allowing interaction with external code.
If a static is `extern`, it cannot have an explicit value, meanwhile `export` static must have an initial value.

Unlike normal statics, both `extern` and `export` statics are allowed to be declared mutable, without needing to rely on interior mutability.
An immutable static must be initialized before any code is executed.

## 7.11. Properties [↵](#7-items-)

```
<property>          := { <attribute> }* [ <vis> ] [ 'unsafe' ] 'property' <name> ( <prop-accessors> | <get-only-accessor> )
<prop-accessors>    := '{' { <prop-accessor> }[1,4] | <get-only-accessor> '}'
<prop-accessor>     := <prop-get> | <prop-ref-get> | <prop-mut-get> | <prop-set>
<prop-get>          := [ <vis> ] 'get' <prop-body>
<prop-ref-get>      := [ <vis> ] 'ref' 'get' <prop-body>
<prop-mut-get>      := [ <vis> ] 'mut' 'get' <prop-body>
<prop-get>          := [ <vis> ] 'set' [ '(' <name> ')' ] <prop-body>
<prop-body>         := <expr-no-block> ';'
                     | <expr-with-block>
<get-only-accessor> := <block-expr>
```

A property allows a field-like value to be associated with a set of expressions that handle the underlying value changes.

Properties are implemented as having either _getters_, a _setter_ or both, these are know as accessors.

Accessors can have their own visibility assigned, although they may not have a broader visibility and may only narrow the visibility of the accessors relative to the property.
If no explicit visibility is provided, the visibility of the property will be used.

A property may also be a so-called direct-bind property, meaning that the property directly refers to a field within the implementing type.
The main use of this is to restrict the use of the given field.
Direct bind propery do not allow a custom implementation and the compiler should emit normal field accesses for these fields.

The program needs to be aware that using properties may result in slower code, depending on the underlying implementation.

Properties can only be declared as associated items.

A property must have at least a getter, a setter, or an observer

### 7.11.1. Getters [↵](#711-properties-)

Getters are used to retrieve a value from the property.

They come in 3 flavors, each having access to the type containing in a different way, as described below:
getter    | kind      | `self` access | return   | remarks
----------|-----------|---------------|----------|----------------------------------
`get`     | value     | `self`        | `T`      |requires the value to be `Clone`.
`ref get` | reference | `&self`       | `&T`     |
`mut get` | mutable   | `&mut self`   | `&mut T` |

### 7.11.2. Setters [↵](#711-properties-)

A setter is used to modify the value of a property, without needing a mutable reference to the value

A setter is provided with a name surrounded by `()` as an argument, and has access to self as `&mut self`

#### Setter shorthand [↵](#7112-setters-)

A setter does not require an explicit name to be provided for the value that it is being set to.
In this case, the implicit `$0` can be used.

### 7.11.3. Observers [↵](#711-properties-)

Observers allow the value within the property to be inspected before and after the value was changed, giving access to both the old and new value.

Observers get access to self as `&self`.

The following observers are available:
observer   | provided value
-----------|----------------
`will_set` | new value
`did_set`  | old value

#### Observer shorthand [↵](#7113-observers-)

Both provided values do not need an explicit

Both allow the

### 7.11.4. Direct-bind properties [↵](#711-properties-)
```
<property-direct-bind>         := { <attribute> }* [ <vis> ] [ 'unsafe' ] 'property' <name> '{' { <property-direct-bound-get-det> }[1,4] '}' ':=' <name> ';'
<property-direct-bind-get-set> := [ <vis> ] [ [ 'mut' ] 'ref' ] 'get'
                                | [ <vis> ] 'set'
```

A direct-bind property is a special version of a property that is directly bound to a value inside of the type it's implemented on.

This allows for access to a member of the type, but also allows access to be restricted based on the property definition.

> _Todo_: allow direct binds to be used in a struct expression, instead of the bound variable (both work, but only one of them)

### 7.11.5. Get-only properties

Get-only properties is some convenience syntactic sugar when a property is only expected be accessed via a `get`.
In this case, the property is directly followed by a block expressions that generates the return value.

### 7.11.6. Trait properties [↵](#711-properties-)

```
<trait-property> := 'property' <name> ':' <type> '{' { <trait-prop-get-set> }[1,4] '}'
<trait-prop-get-set> := [ 'ref' | 'mut' ] 'get' ';'
                      | 'set' ';'
```

An associated trait type declares a signature for an associated propery implementation.
It declares the name, type and which getter/setter combo needs to exist of the property.

Trait implementation cannot implement additional getters/setters.

A default implementation can be provided which will be used when no explicit property is defined within an implementation.
If any setter or getter has a default value, all others are also required to have a default.

When implementing a property, if any getter or a setter is explicitly set, all others are also required to be explicitly defined.

Trait properties cannot require observers, but implementations are free to add them regardless.

## 7.12. Trait [↵](#7-items-)

```
<trait-item> := { <attribute> }* [ <vis> ] [ 'unsafe' ] [ 'sealed' ] 'trait' <name> [ <generic-params> ] [ ':' <trait-bound> ] [ <where-clause> ] '{' { <trait-item> }* '}'
```

A trait represents an abstract interface that type can implement, containing zero or more trait items, like:
- [functions & methods](#736-trait-functions--methods-)
- [type aliases](#743-trait-type-aliases)
- [constants](#791-trait-constant-)
- [properties](#7113-trait-properties-)

All traits define an implicit `Self` type, and refers to "the type that is implementing this trait".
Any generic parameters applied to the trait, are also passed along to the `Self` type

Traits can be implemented via individual implementations.

A trait can be defined as `sealed`, this means that the trait can only be implemented from the current library and any implementation outside of the current library will result in a compile error.

### 7.12.1. Object safety [↵](#712-trait-)

Object safety specifies a set of requirements that the trait needs to adhere to to be allowed to be used in places where a trait object type is allowed.
These are:
- All supertraits must be object safe.
- The trait cannot be sized, i.e. it may not requires `Self is Sized`.
- It must not have associated constants.
- It must not have associated types using generics.
- All associated functions must either be dispatchable from a trait object or be explicilty non-dispatchable.
    - Dispatchable functions must adhere to:
        - Not have any generic parameters.
        - Method is only allowed to use the `Self` type within the receiver.
        - The receiver needs to allow for dynamic dispatch, e.g. `&self` or `&mut Self`, and types implementing `DispatchFromDyn`.
        - Parameters and return types must not be an inferable type, meaning they may not be an impl trait type.
        - May not have a sized bound on the receiver (`Self is Sized` implies this).
    - Explicit non-dispatchable functions require:
        - Have a sized bound on the receiver (`Self is Sized` implies this).

### 7.12.2. Supertraits [↵](#712-trait-)

A 'super trait' is a trait that is required to be implemented by a type to implement a specific trait.
Anywhere a generic or interface object is bounded by a trait, it is also bound by that trait's supertraits.

Supertraits are declared as a trait bound on the `Self` type, and transitively the supertraits of traits declared in their trait bounds.
They can either be defined as a bound directly on the trait, or to `Self` in a where clause.
A trait cannot be its own supertrait, and they cannot form any cyclical supertrait dependence.

### 7.12.3. Unsafe traits [↵](#712-trait-)

Traits can be declared as `unsafe`.
Unsafe traits come with additional requirements that the programmer needs to guarantee is being followed follow.

### 7.12.4. Visibility [↵](#712-trait-)

Traits define their visiblity directly on the trait itself, and all items within the trait take on that visibility.
Individual associated items cannot declare their own visibility.

### 7.12.5 Trait Items [↵](#712-trait-)

```
<trait-item> := <trait-func>
              | <trait-type-alias>
              | <trait-const>
              | <trait-property>
```

Trait items are items that are assocated with a trait.
The following items are supported inside a trait:
- functions
- type aliases
- constants
- properties

Any item that does not have a default value or implementation is required to be implemented in any trait implementation.

## 7.13. Implementation [↵](#7-items-)

```
<impl-item> := <inherent-impl> | <trait-impl>
```

An implementation is an items that associates items with an implementing type.
There are 2 types of implementations:

### 7.13.1. Inherent implementation [↵](#713-implementation-)

```
<inherent-impl> := { <attribute> }* [ <vis> ] [ 'unsafe' ] 'impl' [ <generic-params> ] <type> [ <where-clause> ] '{' { <assoc-item> }* '}'
```

An inherent implementation is defined without specifying a trait to implement.
The type implementing is called the _implementing type_ and the associated items are the _associated items_ of the implementing type.

Inherent implementations associated the contained items ot the implementing type.
Inherent implementaions can support associated functions (including methods), properties, and constants.

The path to an associated item is the path to the implementing type, followed by the associated item's identifier as the final component of the path.

A type can also have multiple inherent implementations.
An implementation for a type must be defined in the same library as the original type definition.

If a visibility attribute is defined for the block, all items with in the block will default to that visibility.
If `unsafe` is added to the block, then all functions within the block will be marked as unsafe.

### 7.13.2. Trait implementation [↵](#713-implementation-)

```
<trait-impl> := { <attribute> }* [ 'extend` ] [ 'unsafe' ] 'impl' [ <generic-params> ] <type> 'as' <trait-path> [ <where-clause> ] '{' { <assoc-item> }* '}'
```

A `trait` implementation is defined like an inherent implementation, but also include the trait to be implemented.

The trait is known as the _implemented trait_, and the _implementing type_ implements the trait.

A trait implementation must define all non-default associated items declared by the implemented trait and it can redefine (i.e. override) an item that has a default implementation.
It is not allowed to define any implementation that is not defined in the implemented trait.

Unsafe traits require the `unsafe` keyword to be added to the implementation.
`trait` implementations are not allowed to specify any visibility for items.

By default, items within a trait implementation cannot be accessed directly and need to be accessed using a trait disambiguation.
Adding `extend` to the implementation allows the items to be directly accessed.

#### Coherence

A trait implementation is coherent when it can be be defined within the current library.

A trait implementation is considered coherent if either the below rules are followed, or there are overlapping implementations.

Two trait implementations overlap when there is 2 implementations ca be instantiated for the same type.

The coherence rules require that the implementation `impl<P0..=Pn> T0 as Trait<T1..=Tn>` to adhere to one of the following:
- `Trait` is a local trait
- At least one type `T0..=Tn` must be a local type 

> _Note_: Coherence rules might be changed in the future

### 7.13.3. Impl field items [↵](#713-implementation-)
```
<impl-field-items>    := <inherent-impl-field> | <trait-impl-field>
<inherent-impl-field> := [ 'vis' ] [ 'unsafe' ] 'impl' [ <generic-params> ] <type> [ <where-clause> ] '{' <assoc-item> '}'
<trait-impl-field>    := [ 'extend' ] [ 'unsafe' ] 'impl' [ <generic-params> ] <type> [ <where-clause> ] '{' <assoc-item> '}'
```

Impl field items are equivalent to their corresponding implementation item, but can be directly declared as an associated item in an item.

### 7.13.4. Associated items [↵](#713-implementation-)
```
<assoc-item> := <fn-item>
             | <method>
             | <const-item>
             | <static-item>
             | <impl-field>
             | <struct-item>
             | <union-item>
             | <enum-item>
             | <bitfield-item>
             | <init-item>
```

Associated items are items which may appear either in distinct implementations, or inline within type bodies, where they act as if they are in an impl.
The following items are allowed:
- Functions
- Methods
- Type aliases
- Statics
- Constants
- Properties

These items can be accessed from the type they are implemented on, below `Item` represents the item that is implemented, `Type` represent the type that has the item implement for it, and `Trait` is the trait the `Type` might be implemented.
- If the type is a path-like type: `Type.Item`
- If the type is not a path type: `(Type).Item`
- If the type implements a trait, which is not marked as `extend`: `Type.(Trait.Item)`

## 7.14. External & export blocks [↵](#7-items-)

```
<extern-block> := { <attribute>* } [ 'safe' ] `extern` <string-literal> '{' <extern-static-item> | <extern-block-fn-item> '}'
<export-block> := { <attribute>* } `export` '{' <extern-static-item> | <extern-block-fn-item> '}'
```

External and export blocks allow their corresponding version of function and static variables to be declared, with all of them having the same attributes applied to them.

An `extern` block can import items from an external libary, they define the name of the library in which the item is located.
The compiler will automatically add the file extension for a given OS.

Extern items will by default be marked as `unsafe`, but can be explicitly marked as `safe`.

An `export` block on the other hand will mark an item for export.

Either can have additional info that is supplied using their corresponding [attributes](#1715-abi-link-symbol-and-ffi-attributes-).
If applied to a block, it will apply to all items within that block.

The items also have their own respective properties as defined for [external functions](#737-external--exported-functions-) and [external statics](#7103-external-statics-).

All items within an external block share the same source library, defined in the block's declaration.

The external block may define the default calling convention using the [`@callconv` attribute](#callconv).

## 7.15. Extensions [↵](#7-items-)
```
<extension-item> := { <attribute>* } [ <vis> ] 'extension' [ <generic-params> ] <type> 'as' <name> [ <where-clause> ] '{' { <assoc-item> }* '}'
```

Extensions allow functionality to be added to am external type, including allow traits to be implemented that when the implementation would not be coherent.
They must be implemented on an external type, adding an extension to a type declared within the current library will result in a compiler error.

Extensions are named and need to be imported explicitly to be able to use any functionality declared in them.

When importing conflicting extensions, meaning that any item within the extension conflicts will also result in a compiler error.
Item are conflicting when:
- the item was implemented in the library the type was defined
- a trait implementation was implemented in the library defining the trait
- the item conflict with an item implemented in another currently imported extension

### 7.15.1. Trait extentions

An extension is a trait extension when the type refer to a given trait.
Doing this allows additional functionality to be added to any type that implements the trait.

This comes a the caveat that all extensions need to have an implementation withing the extension.
This is because when a trait is used as a [trait object type](#11124-trait-object-types-), the extension is not stored within the object's vtable, but is instead placed in a function utilizing the interface that is provided by the trait object itself.

## 7.16. Initializers [↵](#7-items-)
```
<init-item> := <fn-qualifier> 'init' [ '?' | '!' <type> ] 'fn' [ <deduced-params> ] '(' <fn-params> ')' [ <where-clause> ] { <contract> }* <block>
```

An initializer, also known as a init function is a special kind of function that allows for the construction of a type.
Even though they are functions, they act very differently to most functions and therefore have their own section dedicated to them.

An initializer has an implicit return type of `Self` by default, this may be overwritten with either an optional or result type.

An initializer has a set of deduced and regular parameters like a normal function.

Multiple initializers are allowed, but they must be distinguishable by there required parameters, similar to [label-based overloading](#738-label-based-overloading-).

Initializers act as if they are ran on a `&mut MaybeUninit(Self)`, and can therefore also be used with an [in-place operator](#1427-in-place-operator-).
Initializers are also allowed to assign any immutable value within the type they initialize, similar to [constructing expressions](#910-constructing-expression-).

### 7.16.1. Default initialization

Normally, an initializer is required to assign a value to all fields within the type, or it will result in an error.

To help avoid the need to writing repetitive code in different initializer, the initializer can use, so called, default initializion.

When at the end of an initializer, a field has not been assigned, it can do one of two things:
- If a default value is defined for a field and the field will be set to that value
- If a field has a type that implements `Default`, the field will get the value returned by it.

### 7.16.2. Initializer delegation

Initializers can themselves call different initializers, this is called initializer delegation, allow for the re-use of code.

This can be done by calling an initializer of `Self` within the initializer.
This notifies to the compiler to not try and default initialize any other members, and that it is allowed to not fully initialize the item.

If a field would normally get its value for a default initialization, but is assigned in the initializer calling that other initializer, the default assignment will be skipped.

> _Note_: The call needs to be explicitly on `Self`, and not just its type

### 7.16.3. Fallible initializers

Initializers can be fallible, meaning they might fail to initialize.

There are 2 possible variants of fallible initializers:
- `init?`: returns `None` when failing to initialize
- `init!type`: returns an `Err(...)` when failing to initialize. The `!` is followed by the type of the error returned when it fails.

Calling these initializers is done as `T?` or `T!`, where `T` represents the type to initialize.

## 7.17. When items [↵](#7-items-)
```
<when-item>  := 'when' <expr> <item-block> [ 'else' ( <when-item> | <item-block> ) ]
<item-block> := '{' { <item> }* '}'
```

A when item is similar to a [`when` expression](#929-when-expression-), but can be located at a level where only items are allowed.
Any occurance of `when` in a location expressions are allowed will be a [`when` expression](#929-when-expression-).

Similarly, it is controlled using a compile time condition, and does not generate a scope, but instead puts its contents into the same scope as the when item.

This can be thought of as containing code marked with the cfg attribute.

## 7.18. Tests [↵](#7-items-)
```
<test-item>      := `test` <test-tags> <string-literal> [ <test-args> ] [ <test-condition> ] <block>
<test-tags>      := '[' <name> { ',' <name> } ']'
<test-args>      := '(' <fn-params> ')' 'with' {  <expr> ';' }*
<test-condition> := 'when' <expr>
```

A test item can be used to ensure that the program meets the expected behavior.
Test items are only compiled when the project is compiled as a test, and will not be present in code otherwise.

Test items are named, which is the name shown whenever the test is run.
In addition, a set of tags can also be provided, which is used to group different test and only run those tests which have the tag assigned to them.

Test can also be supplied with a list of arguments that the test can be run with.
The test first defines the arguments it takes, and is then followed by a list of argument sets.
An argument set, is a comma separated list of values.
Multiple argument sets can be separated by a `;`.

In addtion, a condition can be provided for when a test is availabe, e.g. only run tests on a given OS or when a given feature is enabled.

Whithin a test, either asserts can be used, or a result can be returned.
Test have an implicit `!impl Display` return type.

Test are run using a special test assert context, allowing them to capture each assert and report it.

By default, the language supplied test harness will be used, but this can be changed for a custom test harness.

> _Note_: In addition, by using the test-supplied allocator, leaks can be reported.

# 8. Statements [↵](#tables-of-contents)

```
<stmt> := <var-decl>
        | <expr-stmt>
        | <defer-stmt>
        | <errdefer-stmt>
```

A statement is a component of a block, which is in turn part of an outer expression or a functions.
Statements differ from expressions, as they do not return a value and can only directly exist within a scope.

## 8.1. Item statement [↵](#8-statements-)

```
<item-stmt> := <item>
```

An item statement is a items which can be defined within a block.
Declaring them at a location other than inside a module, and limits their definition to the block they belong to.
As such, they cannot be referenced outside of the specific scope they are declared in.

They may access items declared outside of the item, but may not access any locally declared values from the scope they are located in.
They can also implicitly capture generics from an outer scope, unless they are shadowed by the generic with the same name by the item.

## 8.2. Variable declaration [↵](#8-statements-)

```
<var-decl> := <name-var-decl> | <let-var-decl>
<name-var-decl> := <var-decl-name> { ',' <var-decl-namef> }* ':=' <expr> ';'
<var-decl-name> := [ 'mut' ] <name>
<let-var-decl> := 'let' <pattern-top-no-alt> [ ':' <type> ] [ '=' <expr> [ 'else' <block-expr> ] ] ';'
```

A variable declaration introduces (a) new variable(s) withing a scope.
By default, variables are immutable and need to explicitly be defined as `mut` to be able to be mutated.

If no type is provided, the compiler will infer the type from the surrounding context, or will return an error if insuffient information can be retreived from code.

Any variable introduced will be visible until the end of the scope, unless they are shadowed by another declaration.

When using a 'name' variable declaration with more than 1 name, the expression must be one of the following:
- An comma separated list of expression (i.e. a comma expression)
- A tuple expression
- An expression returning a tuple
If either of the latter 2 is provided, the declaration will automatically destructure the tuple into the variables.
The number of elements within any of the expressions need to match the number of names provided, otherwise a compiler error will be generated.

When using a `let` variable declaration, a variable may also be declared as being unitialized, this requires:
- An explicit type to be given
- Only identifiers or tuple patterns are provided
- The variable needs to be assigned a value in all possible paths that can reach the first use of that variable.

A `let` variable declaration may also contain an `else` block, allowing a refutable pattern.
If this patten fails to match, the else block will get executed, this is generally used to either panic or return from the function.
If an `else` block is not present, the pattern needs to be irrefutable.

## 8.3. Expression statement [↵](#8-statements-)

```
<expr-stmt> := <expr-no-block> ';'
             | <expr-with-block>
```

An expressions statement evaluates a given expression and ignores the result.
As a rule, an expression statement's purpose is to trigger the effects of evaluating its expression.

If an expression ends with a block, and if used in a context where a statement is permitted, the trailing semicolon can be omitted.
This could lead to ambiguity, when this can be parsed as both part of a larger expression or as a standalone expression, it will be parsed as a statement.

The implicit return type of a statement at the end of a function is a unit type.

## 8.4. Defer statement [↵](#8-statements-)

```
<defer-stmt> := 'defer' <expr-with-block>
              | `defer` <expr-no-block> ';'
<err-defer-stmt> := 'errdefer' [ <err-defer-capture> ] <expr-with-block>
                  | `errdefer` [ <err-defer-capture> ] <expr-no-block> ';'
<err-defer-capture> := '|' [ 'mut' ] <name> '|'
```

A defer expressions delays the execution of an expression until the end of the scope, but before any variables are being dropped.
Defers are evaluated in the reverse order they are called, in a so-called LIFO (Last-In First-Out) order.

### 8.4.1. Defer-on-error statement [↵](#841-defer-on-error-statement-)

A defer-on-error statement is a variation of a defer statement.
Unlike a normal defer statement, it only defers when the function is returned by either a [propagating try operator](#1431-propagating-try-) or a [throw expression](#926-throw-expression-).
Defer-on-error will only be evaluated if the error defer is in the current scope, meaning that if a scope is exited, the defer-on-error will not be executed when one of the above expressions cause an error.

Evaluating error defers can be avoided by explicitly returning an erronous value.

A defer-on-error statement can also capture a reference or mutable reference of the resulting error to be used inside of the error defer's body.

## 8.5. Guard statements
```
<guard-stmt> := 'if' <expr> 'else' <block>
```

A guard statement is similar to an [`if` statement](#918-if-expression-), but does the opposite.
A guard statement only has an else block, which defines what should happen when the expression results in a `false` value.
Since they do not have a regular body, they are not counted as expressions

> _Note_: the reason this is a statement and not an expression, is because unlike an `if`, a `guard if` cannot return a value, since it only executes on a `false` value

# 9. Expressions [↵](#tables-of-contents)

```
<expr> := <expr-with-block> | <expr-no-block>
<expr-with-block> := <block-expr>
                   | <if-expr>
                   | <loop>
                   | <match-expr>
<expr-no-block> := <literal-expr>
                 | <path-expr>
                 | <unit-expr>
                 | <operator-expr>
                 | <paren-expr>
                 | <in-place-expr>
                 | <type-cast-expr>
                 | <type-check-expr>
                 | <contructing-expr>
                 | <index-expr>
                 | <tuple-index-expr>
                 | <call-expr>
                 | <method-call-expr>
                 | <field-access-expr>
                 | <closure-expr>
                 | <closure-arg-expr>
                 | <op-method-expr>
                 | <trailing-closure-expr>
                 | <full-range-expr>
                 | <break-expr>
                 | <continue-expr>
                 | <fallthrough-expr>
                 | <return-expr>
                 | <underscore-expr>
                 | <throw-expr>
                 | <comma-expr>
                 | <when-expr>
                 | <template-string-expr>
                 | <key-path-expr>
                 | <await-expr>
                 | <yield-expr>
                 | <meta-expr>
```

Expressions do 2 things:
- create a value
- produce a side-effect

Each expression will return the value produced by it, while also applying any effect during evaluation.
An expression can contain multiple sub-expressions, which are called the operands of an expression.

Each expression dictates the following:
- Whether or not to evaluate the operands when evaluating the expression.
- The order in which to evaluate the operands
- How to combine the operands' values to obtain the value of the expression.

In this way, the structure of the expression dictates the structure of execution.

The precedence of expressions follow the order defined below

expressions                                                     | associativity
----------------------------------------------------------------|---------------
paths                                                           |
method calls                                                    |
field accesses                                                  | left-to-right
functions calls, indexing                                       | left-to-right
`try`, `catch`                                                  |
comma expression arguments to operators                         | left-to-right
operators (see the [precedence section](#15-precedence-))       |
other comma expression                                          | left-to-right
`return`, `break`, `continue`, `fallthrough`, `throw`, closures |

In general, the operands of an expression will be evaluated before any side-effects will be applied, and the operands are evaluated from left to right.
Each expression that deviates from this order, will define if and in which order their expressions are evaluated.

## 9.1. Expression details [↵](#9-expressions-)

### 9.1.1 Place, value & assign expressions [↵](#91-expression-details-)

Expressions can be divided in 3 categories:
- Place expressions.
- value expressions.
- Assign expressions.

With each expression, operands may likewise occur in either place or value context.
The evaluation of an expression depends both on its own category and the context it occurs in.

#### Place expressions [↵](#911-place-value--assign-expressions-)

A place expression represents an expression that points to a location in memory.

They refer to the following expressions:
- Local variable, like a path
- Static variables, like a path
- Dereferenced addresses or references
- Indexing resulting in a place expression
- Field references
- Parenthesized place expressions
- Any call (function and operator) that results in a place expression
- Any property resulting in a place expression

#### Assign expressions [↵](#911-place-value--assign-expressions-)

An assign expression is any expression which can appear on the left hand side of an assignment operator.

They refer to the following expressions:
- Place expressions
- Underscores
- Tuples of assign expressions
- slices of assign expressions
- Tuple structs of assign expressions
- Aggregate structs of assign expressions (with possible named fields).
- Unit structs

#### Value expressions [↵](#911-place-value--assign-expressions-)

Any expression that does not fit [place](#place-expressions-) or [assign](#assign-expressions-) expressions are value expressions

### 9.1.2. Moved & copied types [↵](#91-expression-details-)

When a place expression is evaluated as a value expression, or is bound to a value expression in a pattern, it denotes the value held in that memory location.
If the type is copyable, then the value will be copied, otherwise if the value is sized, it is moved.
Only the following place expressions can be moved out from:
- variables that are not currently borrowed
- temporary fields
- fields of place expressions that can be moved out of, if the field does not need to be dropped or used in a drop implementation, i.e. if the field can be partially moved
- Result of a expressions that supports moving out of, i.e. a type that implements `DerefMove`.

After moving out of a place expression that is evaluated in a local expression, the location is then deinitialized and cannot be read from again until it is reinitialized.

In all other places, a place expression in a value expression will result in an error.

### 9.1.3. Mutability [↵](#91-expression-details-)

For a place expression to be able to be assigned to, it needs to be mutable, either by being mutably referenced (either explicitly or implicitly), or must be explicitly refered to as mutable in a pattern.
These are called _mutable place expression_, all other place expressions are _immutable place expressions_

The following expressions can be used as mutable expressions:
- Mutable variable that is currently not borrowed.
- Temporary values
- Fields
- Dereferences of mutable pointers, i.e. `^mut T`
- Dereferences of a variable or a field of one, with a type of `&mut T`
- Dereferences of types supporting mutable dereferences, i.e. when the type implements `DerefMut`
- Any expressions that results in a place expression that is mutable

### 9.1.4. Temporaries [↵](#91-expression-details-)

When using a value expression in a location a place expression is expected, a temporary unnamed memory location is created (usually on the stack) and is set to the value of the expression creating the temporary.
The temporary value will then be used as the place expressions and will be dropped at the end of the expression's scope.

### 9.1.5 Implicit borrows [↵](#91-expression-details-)

Certain expressions will treat an expression as a place expression by implicitly borrowing it.

Implicit borrowing takes place in the following expressions:
- Left operand in a method call
- Left operand in a field expression
- Left operand in a call expression
- Left operand in an index expression
- Operand of a derefence operator
- Operands of a comparison
- Left operand of a compound assignment

## 9.2. Literal expression [↵](#9-expressions-)

```
<lit-expr> := ? <literal> except <int-decimal-literal> ? [ [ "'" ] <name> ]
            | <int-decimal-literal> [ "'" <name> ]
```
A literal expression consists of a literal token (or multiple in case of multi-line strings), that denotes the value it will evaluate to.
It is similar to a constant value, and is (primarily) evaluated at compile time.

In addition, literal expression can also invoke a literal operator to be applied of them, which can either be evaluated at compile time, in case of a fixed size type, but may also evaluate at runtime, for example when needing to allocate backing memory.
Literal operators may have an effect on which values the contents are allowed to be.

In most cases, the literal operator can be applied by directly appending it to the end of the literal.
The integer decimal literal is an exception, as `3m` will be interepreted as a name and not as a literal `3`, followed by a literal operator.
In this case, a `'` (single quote) can be used, resulting in the example being `3'm`, this can be done as 2 literals can never follow each other.

A `'` (single quote) is allowed for other literals, but it not neccesary.

If a signle quote is used, it must be directly attached to the literal and may not have any whitespace.

### 9.2.1. Literal type conversion [↵](#92-literal-expression-)

Each literal defined within the [Literals section](#6-literals-) has its own literal representation, which itself cannot be the type of the literal expression.
Therefore each literal needs to be converted to a type that can be used.

If a literal operator is used, this is fairly simple, as the value will be of the type returned by the literal operator.

If none is used, the compiler needs to figure out the best possible type. If this can be derived from the surrounding environment, the value will be implicitly converted.
Each kind of literal can be converted into a set of type defined below:

Literal kind                 | Possible types
-----------------------------|----------------
Integral decimal literal     | [Signed integers](#signed-types) and [Unsigned integers](#unsigned-types)
Float decimal literal        | [Floating point](#floating-point-types)
Binary literal               | [Signed integers](#signed-types) and [Unsigned integers](#unsigned-types)
Octal literal                | [Signed integers](#signed-types) and [Unsigned integers](#unsigned-types)
Integral hexadecimal literal | [Signed integers](#signed-types) and [Unsigned integers](#unsigned-types)
Float hexadecimal literal    | [Floating point](#floating-point-types)
Boolean literal              | [Booleans](#boolean-types)
Character literal            | [Characters](#character-types)
String literal               | [String slices](#11110-string-slice-types-)

In case the compiler cannot figure out the type from surrounding context, it will default to the following:

Literal kind                 | Default types
-----------------------------|----------------
Integral decimal literal     | `i64`
Float decimal literal        | `f64`
Binary literal               | `u64`
Octal literal                | `u64`
Integral hexadecimal literal | `u64`
Float hexadecimal literal    | `f64`
Boolean literal              | `bool`
Character literal            | `char`
String literal               | `str`

## 9.3. Path expression [↵](#9-expressions-)

```
<path-expr> := [ <path-start> ] <iden>
             | 'self'
```

A path expression only represents the start of a path that refers to a local variable or item.
All path elements following this will be parsed as field accesses and method calls when generics are used.

Path expressions referencing local or static variables are place expression, all other path expressions are value expressions.

A path may also refer to an inferred path, which is represented by a `.`, followed by a name.
This is currently limited to enum members when the enum type can be inferred from the surrounding context.

`self` is a special case of a path expr, as it represents the receiver of a method.

## 9.4. Unit expression  [↵](#9-expressions-)

```
<unit-expr> := '(' ')'
```

A unit expressions is an empty expression that does nothing and returns a unit type.

## 9.5. Block expression  [↵](#9-expressions-)

```
<block-expr> := <block>
              | <unsafe-block>
              | <const-block>
              | <async-block>
<block>      := '{' { <stmt> }* [ <expr> ] '}'
```

A block expression creates a new anonymous scope within an expression, allowing more than just expressions to be defined in a location normally only a single expressions would be allowed.
A block executes its non-item components and then its last optional expression.
Any items or local variable in the scope only live for the length of the scope and are not accessible outside of the scope.

The block can contain a final expression that is not ended by a semicolon, this will implicitly return its value from the block.

Blocks allow for the arbitrary nesting of code, meaning that it allows statements, expressions, and items.

Blocks are always value expressions.

If a block ends in comma expression, then the block will return a tuple formed by that comma expression.

There are 3 special types of block expressions:

> _Todo_: For more info on labelled blocks, see their [relavent section](#9195-labelled-block-expressions-)

> _Todo_: Once `sync` is supported, add an async block

### 9.5.1. Unsafe block [↵](#95-block-expression--)
```
<unsafe-block> := 'unsafe' <block>
```

An `unsafe` block will run the entirety of its code within an unsafe constext, allowing unsafe operation to be called within it.

### 9.5.2. Const block [↵](#95-block-expression--)
```
<const-block> := 'const' <block>
```

A `const` block will execute its code at compile time and will become an inline constant value after compilation.

They are equivalent to a contant item, but can be declared inline, and can therefore also be referred to as _inline consts_.
They have the benefit of supported the surrounding type inferrence to determine the value's type.

Unlike [conts items](#79-const-item-), they may refer to surrounding values and generic parameters.

### 9.5.3. Async blocks [↵](#95-block-expression--)
```
<async-block-expr> := 'async' <block>
```

An unsafe block is a version of a block which evaluates into a future.
The final expression within the block is used to determine the return type of the future generated by the block.

Async blocks produces a type which implements the `Future` trait.

#### Capture modes [↵](#95-block-expression--)

Async blocks need to capture surrounding variables, it uses this in the same manner as [closures](#capture-modes--1).
In addition, an async move may explicitly be declares as `move`, mimicking the `move` capture mode of closures

#### Async context [↵](#95-block-expression--)

Since an async block creates a future, they define an async context which can then contain `await` expressions.

#### control-flow operators [↵](#95-block-expression--)

Async blocks act like function boundaries, just like closures.

As a result, any expressions that return from a function are applied on the async block, and not the surrounding function.

The boundary also means that any expression like `break` and `continue` cannot affect branches outside of the async block.

## 9.6. Operator expression  [↵](#9-expressions-)

```
<op-expr> := <prefix-op> <expr>
           | <expr> <postfix-op>
           | <expr> <infix-op> <expr>
```


An operator expression applies operators on 1 or 2 sub-expressions.
The resulting value of these expression will depend on the implementation of the operators.

When calling a prefix of postfix operator, the operator needs to be directly next to the expression it applies to and may not be separated by space.
When it comes to infix operators, they may be placed between sub-expressions without spaces, as this means there there is not pre- or postfix expression within the either operand.
Otherwise, if a post or prefix expression must be used, it must not be directly placed against the another expression, but must be separated with a space.

Prefix and postfix operators can only chained when the by using parenthesized expression, meaning that chaining 2 `-`s requires the following to be written: `-(-val)`.

For additional info on operators, check the [Operator section](#14-operators-).

> _Note_: Is it generally preferred to have spaces around infix operators regardless of when a prefix of postfix expression is on either side.

## 9.7. Parenthesized expression  [↵](#9-expressions-)

```
<paren-expr> := '(' <expr> ')'
```

A parenthesized expression, also known as a grouped expression, wraps a single expression, allowing the expression to be evaluated before any other expressions that are outside of the parentheses will be executed.

Parenthesized expressions can be both place and value expressions, depending on the expression within parentheses.

Parentheses explicitly increase the precedence of this expression above that of other expressions, allowing expressions that would have a lower precedence to be executed before outer expressions use this expression.

## 9.8. Type cast expression  [↵](#9-expressions-)

```
<type-cast-expr> := <expr> <as-op> <type>
<as-op> := 'as' | 'as?' | 'as!'
```

A type cast expression is a special binary operator, which has a type on the right hand side.

Executing the expression will cast the value on the left hand side to the type on the right hand side.

The cast comes in 3 forms:
- `as`: converts directly to a different type, expect all convertions between the types to be valid
- `as?`: tries to convert to a different type, return `.Ok(...)` when it succeeds, and an `.Err(...)` when it fails to
- `as!`: tries to convert to a different type, panics when it fails to


Each cast expression has traits associated with them:
cast  | from trait   | to trait 
------|--------------|-----------
`as`  | `From`       | `To`
`as?` | `TryFrom`    | `TryTo`
`as!` | `UnwrapFrom` | `UnwrapTo`

The from, to traits represent directional cast traits, and are as following:
- the from trait is implemented on the type being cast
- the to trait is impleented on the type being cast to

If either of these traits is implemented, the other will automatically be generated.

### 9.8.1. Builtin casts [↵](#98-type-cast-expression--)

The builtin cast `as` can be used to explicitly perform coercions, as well as the following casts.
Any cast that does not fit either a coercion rule, an entry in the table below, or a user-defined conversion will generate a compiler error.
Here `^T` means either `^T` or `^mut T`. `m_` stands for an optional `mut` in reference and pointer types.

If a cast is set as bidirectional, it means that a value of `U` can also be cast to type `T`

Type of `e`                         | `U`                                 | Cast performed by `e as U`               
------------------------------------|-------------------------------------|------------------------------------------
Integer or Float type               | Integer or float type               | [Numeric cast](#numeric-cast-semantics-)
Enumeration                         | Integer type                        | [Enum cast](#enum-cast-semantics-)
Boolean or character type           | Integer type                        | [Primitive to integer cast](#primtive-to-integer-cast-semantics-)
`u8`                                | Character type                      | [Integer to character cast](#integer-to-character-cast-semantics-)
`^T`                                | `^U` where `U` is sized *           | [Pointer to pointer cast](#pointer-to-pointer-semantics-) †
`^T` where `T` is sized             | Integer type                        | [Pointer to address cast](#pointer-to-address-cast-semantics-) †
`&m1 T`                             | `^m2 T` **                          | [Reference to pointer cast](#reference-to-pointer-casts-) †
`&m1 [N]T`/`^m1 [N]T`               | `^m2 T`/`[^]m2 T` **                | [Array to pointer cast](#array-and-slice-to-pointer-casts-) †
`&m1 [N]T`/`^m1 [N]T`               | `^[]T` **                           | [Array to slice pointer cast](#array-and-slice-to-pointer-casts-) †
`^m1 []T`/`^m1 []T`                 | `^m2 T`/`[^]m2 T` **                | [Array to pointer cast](#array-and-slice-to-pointer-casts-) †
Function item                       | Function pointer                    | [Function item to function pointer cast](#function-item-casts-semantics-) †
Function item                       | `^U` where `U` is a supported type  | [Function item to pointer cast](#function-cast-semantics-) †
Function item                       | Integer                             | [Function item to address cast](#function-cast-semantics-) †
Function pointer                    | `^U` where `U` is a supported type  | [Function pointer to pointer cast](#function-cast-semantics-) †
`^T` where `T`  is a supported type | Function pointer                    | [Pointer to function pointer cast](#function-cast-semantics-) 
Function pointer                    | Integer                             | [Function pointer to address cast](#function-cast-semantics-) †
Closure ***                         | Function pointer                    | [Closure to function pointer cast](#function-cast-semantics-) †
`T`                                 | Opaque type with same size as `T`   | [Type to opaque cast](#type-to-opaque-cast-semantics-)
`^T`                                | `^U` where `U` is an opaque type    | [Type to opaque cast](#type-to-opaque-cast-semantics-)
`&m1 T`                             | `&m2 U` where `U` is an opaque type | [Type to opaque cast](#type-to-opaque-cast-semantics-)
Opaque type with same size as `U`   | `U`                                 | [Opaque to type cast](#opaque-to-type-cast-semantics-) †
`^T` where 'U' is an opaque type    | `^U`                                | [Opaque to type cast](#opaque-to-type-cast-semantics-) †
`&m1 T` where 'U' is an opaque type | `&m2 U`                             | [Opaque to type cast](#opaque-to-type-cast-semantics-) †

\* or `T` and `U` are compatible unsized types, e.g. both slices, both are the same interface 

\** only when `m1` is `mut` or `m2` is `const`. Casting `mut` reference to `const` pointer is allowed.

\*** only for closure that do not capture (close over) any local variables

† Casts are unsafe

> _Note_: casting an integer type to a pointer is only allowed via the appropriate library functions

#### Numeric cast semantics [↵](#981-builtin-casts-)
- Casting between two integer types of he same size (e.g. i32 -> u32) is a no-op (2's complement is used for negative numbers)
- Casting from a larger integer to a smaller integer (e.g. u32 -> u8) will truncate
- Casting from a smaller integer to a larger integer (e.g. u8 -> u32) will
  - Zero extend when the source is unsigned
  - Sign extend when the source is signed
- Casting from a float to an integer will round the float towards zero
  - `NaN` will return 0
  - Values larger than the maximum value, including `INFINITY`, will saturate to the maximum value of the integer type
  - Values samller than the minimum integer value, including `-INFINITY`, will saturate to the minimum value of the integer type
- Casting from an integer to a floating point will produce the closest possible float *
  - if necessary, rounding is according to the currently set [rounding mode](#rounding)
  - on overflow, infinity (of the same sign as the input) is produced
  - note: with the current set of numeric types, overflow can only happen on `u128 as f32` for values greater or equal to `f32::MAX + 0.5`, for for casts to an `f16`
- Casting from an f32 to an f64 is perfect and lossless
- Casting from an f64 to an f32 will produce the closest possible f32 value **
  - if necessary, rounding according to `roundTiesToEven` mode ***
  - on overflow, infinity (of the same sign as the input) is produced

\* if integer-to-float casts with this rounding mode and overflow behavior are not supported natively by the hardware, these casts will likely be slower than expected.

\** If f64-to-f32 casts with this rounding mode and overflow behavior are not supported natively by the hardware, these casts will likely be slower than expected.

#### Enum cast semantics [↵](#981-builtin-casts-)

Casts from an enum to its distriminant, then uses a numeric cast if needed. Casting is limited to the following kinds of enumerations:
- Unit-only enums
- Field-less enums without explicit discriminants, or where only unit variants have explicit discriminants
- Flag enums

#### Primtive to integer cast semantics [↵](#981-builtin-casts-)

- `false` casts to 0, `true` casts to 1.
- character types cast to the value of the code point, then uses a numeric cast if needed.

#### Integer to character cast semantics [↵](#981-builtin-casts-)

Casts a `u8` corresponding to a character type, then cast to a character type with the corresponding codepoint.
To convert from larger integers, use `as?`, `as!`, or any of the provided functions.

#### Pointer to address cast semantics [↵](#981-builtin-casts-)

Casting from a raw pointer to an integer produces the machine address of the referenced memory.
If the integer type is smaller than the pointer type, the address may be truncated; using `usize` avoids this.

#### Pointer to pointer cast semantics [↵](#981-builtin-casts-)

`^T`/`^mut T` can be cast to `^U`/`^mut U` with the following behavior:
- If `T` and `U` are both sized, the pointer returned is unchanged.
- If `T` and `U` are both unsized, the pointer is also returned unchanged. In particular, the metadata is preserved exactly.
  If `T` and `U` are objects, this does require that they are compatible types, e.g. same non-marker interfaces.

  For instance, a cast from `^[]T` to `^[]U` preserves the number of elements.
  Note that, as a consequence, such casts do not neccesarily preserve the size of the pointer's reference (e.g. casting `^[u16]` to `^[u8]`) will result in a raw pointer which refers to an object of half the size of the original.
  The same holds for `str` and any compound type whose unsized tail is a slice type, such as `struct Foo(i32, [u8])` or `(u64, Foo)`
- If `T` is unsized and `U` is sized, the cast discards all metadata that completes the wide pointer `T` and produces a thin ponter `U` consisting of the data part of the unsized pointer.

#### Reference to pointer casts [↵](#981-builtin-casts-)

References may be directly cast to a pointer, but this loses compiler information about the reference's capture status.
Because of this, the reverse of this cast requires a pointer re-borrow.

#### Array and slice to pointer casts [↵](#981-builtin-casts-)

Arrays me be cast to a slice pointer, this will be equal to the following: `&arr[..] as ^[]T`, resulting in a pointer to the slice with the lenght of the array.

When an array or a slice are cast from a reference or pointer, the following semantics are used:
- if cast to a single element pointer, the pointer will point to the first element and only that element will be available
- if cast to a multi element pointer, the pointer will point to the first element, with the other values accessible from there
  It is up to the user to ensure the array or slice is not accessed out of bounds.

#### Function cast semantics [↵](#981-builtin-casts-)

Function items can be cast to a function pointer, and have the same semantics as function pointers for other casts.

When casting from function items, or to and from function pointers, they require that the non-function pointer that is being cast to or from is one of the following:
- A trait object to one of the following:
    - `Fn`
    - `FnMut`
    - `FnOnce`
- A unit type

Non-capturing closures can be cast to function pointers with a matching signature.

> _Todo_: Check if the function traits mentioned are correct

#### Type to opaque cast semantics [↵](#981-builtin-casts-)

Any type may be cast to its corresponding opaque type, meaning that:
- `T` can directly be cast to a size opaque type of the same type
- `^T`/`^mut T` can directly be cast to a `^U`/`^mut U`, where `U` is either an unsized, or same sized opaque type
- `&T`/`&mut T` can directly be cast to a `&U`/`^mut U`, where `U` is either an unsized, or same sized opaque type

`T` may either be a sized or unsized type when casting from pointers or references.

#### Opaque to type cast semantics [↵](#981-builtin-casts-)

Any opaque type may be cast to its corresponding non-opaque type, meaning that:
- `T` can be directly cast to a type `U` that is sized
- `^T`/`^mut T` can directly be cast to a `^U`/`^mut U`, where `T` is either an unsized type, or same sized opaque type as `U`
- `&T`/`&mut T` can directly be cast to a `&U`/`&mut U`, where `T` is either an unsized type, or same sized opaque type as `U`

> _Note_: Since there is not guarantee that the underlying data in the opaque type is valid, this is an `unsafe` operation

### 9.8.2. Try and unwrap casts [↵](#99-type-cast-expression--)

A try cast `as?` can be used to cast a type from an interface object, impl interface object, or a generic to a given type, returning an optional type with a valid value when the cast is possible and a `None` when it's not.
This can therefore be used to dynamically handle code based on a type when RTTI info is available.

An unwrap cast `as!` is similar to a try cast, but meant for in usecases the user is certain that the cast is possible, as it will unwrap the resulting nullable type.
This could also be written as `(a as? T)!`.
By default, it will panic when the cast is not available, but in certain configuration, this can be changed into a cast that always passes, so may return in UB if not used correctly.

Any cast that happens on a generic or impl interface object will be resolved at compile time.

## 9.9. Type check expression  [↵](#9-expressions-)

```
<type-check-expr> := <expr> <is-op> <type>
<is-op> := 'is' | '!is'
```

A type check expression is a special binary operator, which has a type on the right hand side.
A type check expression can be used for the following:
- if an impl trait object, or a generic is of a given type
- if a trait object is a given type, when runtime type info is enabled
- if an object, reference to an object, or a pointer to an object, implements a given interface

This check can only occur on place expressions.

There is both a positive and negative version of this expression.

When the positive version is used in the condition of a conditional expression, and it is the only type check experssion on this value, the value will be implicitly promoted within the block that gets executed when the condition is true.

Any check that happens on a generic or impl interface object will be resolved at compile time.

## 9.10. Constructing expression [↵](#9-expressions-)

```
<constructing-expressions> := <tuple-expr>
                            | <array-expr>
                            | <aggregate-expr>
```

A constructing expression constructs a new instance of a type.
This consists of a group of multiple expressions and can also be used in [in-place expressions](#98-in-place-expression--).

### 9.10.1. Tuple expression [↵](#910-constructing-expression-)

```
<tuple-expr>     := '(' <tuple-elem> { ',' <tuple-elem> }+ ')'
<tuple-elem>     := [ <tuple-elem-idx> ':' ] <expr>
<tuple-elem-idx> := <int-decimal-literal> | <expr>
```

A tuple expression constructs a tuple value.

The construction exists out of a comma separated list of values that need to be placed within the tuple.
Since 1-ary tuples are not supported, if the expression only contains 1 operand, it will be interpreted as a [parenthesized expression](#97-parenthesized-expression--).
Similarly if the expressions contains 0 operands, a unit type will be created.

The number of operands within the tuple initializer defines the arity of the tuple.
When initializing a tuple, the operand will be evaluated in the order they are written, i.e. left-to-right.
Each operand will be assigned to the field they represent within the expression, i.e. the first operand will be assigned to field '0', and so on.

Tuple values may also be explicitly set using an index for normal tuples, and names for named tuples.
All elements need to be assigned with an index when this method is utilized.

Tuple expressions are value expressions.

### 9.10.2. Array expression [↵](#910-constructing-expression-)

```
<array-expr>       := <array-list-expr> | <array-count-expr> | <array-list-comprehension>
```

An array expression constructs arrays, and come in 2 forms.

Creating a multi-dimensional array can be done by nesting array expressions within other array expression, i.e. `[[..], [..], [..]]` will result in a 2D array of type `[N][M]T`.

#### Array list expression [↵](#9102-array-expression-)
```
<array-list-expr>  := '[' ( <array-list-elem> { ',' <array-list-elem> }* [ ',' ] ) ']'
<array-list-elem>  := [ <expr> ':' ] <expr>
```

The first form lists out all values in the array, this is represented as a comma separated list of expressions.
Each expression is evaluated in the order that they are written, i.e. left-to-right.

Elements within a list expression may explicitly given an index (for normal arrays), or an enum variant (for enumerated arrays) that each value corresponds to.
When this is done, it is allowed to skip elements, which will be initialized by the array element type's default value, i.e. for an array of `[N]T`, the default value will be `T.default()`
List may be initialized out-of-order using this.

If each element consists out of 2 expressions, separated by a colon, they will on of the following, depending on the value on the left of the colon, also known as the key:
- if the key consists out of integer literals, they will be the indexes of the element that is set to the given value
- if the key consists out of paths pointing to variants of an compatible enum, it will result in an enumerated array
- any other set results in an array of key-value pairs of type `KeyValue(K, V)`.

Key-value pairs starting with one of the above is still possible, but requires the compiler to be able to infer that the resulting type needs to be an array of key-value pairs.

If a the left expression is a range, the range is of a type described above that does not generate a key-value pair, it means that the value after it will be assigned to that range of elements.
This does require that the value must be a copyable value, point to a constant item, or be `None`.

#### Array count expression [↵](#9102-array-expression-)
```
<array-count-expr> := '[' <expr> ';' <expr> ']'
```

The second form consists out of 2 expression separated by a `;`.
The expression on the left is called the 'repeat' operand, the expression on the right the 'count' operand.
The count operand must be able to be evaluated at compile time and have a `usize` type.
This form creates an array with a length of the value of the count operand, with each value being a copy of the value evaluated from the repeat operand.
This means that `[a;b]` creates an array of `b` elements with the value `a`.
If the value of the count operand is larger than 1, the repeat operand must be copyable, point to a constant item, or be `None`.

Array count expressions do not support key-value pairs.

#### Array comprehension [↵](#9102-array-expression-)
```
<array-list-comprehension> := '[' <list-comprehension> ']'
<list-comprehension>       := <list-comprehension-elem> 'for' <pattern> 'in' <expr> [ 'if' <expr> ]
<list-comprehension-elem>  := [ <expr> ':' ] <expr>
                            | <list-comprehension> 
```

A list commprehension is a special `for`-style syntax, allowing an arbitrary list of values to be generated programatical.
It consists of a value that will be produced, a `for`-like loop iterating through a set of value, and an optional trailing condition.

If each element consists out of 2 expressions, separated by a colon, they will on of the following, depending on the value on the left of the colon, also known as the key:
- if the key consists out of paths pointing to variants of an compatible enum, it will result in an enumerated array
- any other set results in an array of key-value pairs of type `KeyValue(K, V)`.

If each element consists out of 2 expressions, separated by a colon, they will be key-value pair of type `KeyValue(K, V)`.

In the special case where the value is of an optional type, if the condition checks if an optional value of type `?T` with the expression `expr?`, and the name is used within the value's expression, the elements of the array will be of the sub-type of that optional, i.e. `T`.

### 9.10.3. Struct expressions [↵](#910-constructing-expression-)

```
<struct-expr> := <struct-expr-path> '{' [ <struct-expr-member> { ',' <struct-expr-member> }* [ ',' [<struct-complete>] ] ] '}'
<struct-expr-path> := <path> | '.'
<struct-expr-member> := [ <name> ':' ] <expr>
<struct-complete> := '..' [ <expr> ]
```

A struct expression creates a structure, enum, or union value.

#### Struct (& union) expression [↵](#9103-struct-expressions-)

A struct expressions with fields enclosed in curly braces allows the specifying of values for each individual field in the structure.

A union is created as a struct expression with only a single field.

##### Functional update syntax [↵](#9103-struct-expressions-)

An struct expression that constructs a value of a struct type can terminate with a `..` followed by an optional expression.

This is used to fill in any fields that were not explicitly specified in the struct expression.

If this expression is given, it must evaluate to a type, which contains fields that match all non-specifier fields in the structure that is being created.
It then moves of copies these missing fields from the value into the structure being created.
As with all struct expressions, all of the fields of the struct must be visble in the current scope, even those not explicitly named.

If no value is provided, all the fields of struct `T` will be extended by their default values declared in `T.default()`, this allows default values to be created at runtime.

Using this expression will also overwrite all default fields.

##### Struct field shorhand [↵](#9103-struct-expressions-)

When initializing an struct value with named fields, it is allowed to write `fieldname` instead of `fieldname: fieldname`.
This allows for a more compact syntax with less duplication.

##### Default fields [↵](#9103-struct-expressions-)

When a struct has default field values, they are not required to assign a value to those fields.
If they are not provided, the default field value will be assigned.

#### Tuple struct expression [↵](#9103-struct-expressions-)

An struct expression with fields enclosed in parentheses constucts a tuple struct.
Though listed here as specific experession, this is equivalent to the a call expression to the tuple struct's pseudo-constructor.

#### Unit struct [↵](#9103-struct-expressions-)

A unit struct expression creates is either just a path or an implied path.
This refers to the unit struct's implicit constant of it's value.
The unit struct value can also be constructed in 2 ways:
- as a path
- as a fieldless struct expression

### 9.10.0. Vector expression
```
<vec-expr> := '[<' <expr> { ',' <expr> }* [ ',' ] '>]'
```

A vector expressions allows the creation of a vector type.

This comes in 2 forms:

#### Vector list expression
```
<vec-list-expr> := '[<' <expr> { ',' <expr> }* [ ',' ] '>]'
```

The first form lists out all values in the vector, this is represented by a comma separated list of expressions.
Each expression is evaluated in the order that they are written, i.e. left-to-right.

The number of elements must be less or equal to a supported size by the vector.
This may also be a range expression, in this case, the vector will contain all values within this range.

If less elements are provided than the vector's size, the remaining values will be filled by `T.default()`.

#### Vector count expression
```
<vec-count-expr> := '[<' <expr> ';' <expr> '>]'
```

Similarly to he second form of an array expression, it consists out of 2 expression separated by a `;`.
The expression on the left is called the 'repeat' operand, the expression on the right the 'count' operand.
The count operand must be able to be evaluated at compile time and have a `usize` type.
This form creates an array with a length of the value of the count operand, with each value being a copy of the value evaluated from the repeat operand.
This means that `[<a;b>]` creates a vector of `b` elements with the value `a`.
If the value of the count operand is larger than 1, the repeat operand must be copyable, point to a constant item, or be `None`.

The count must be less or equal to a supported size by the vector.
If less elements are provided than the vector's size, the remaining values will be filled by `T.default()`.

## 9.11. Index expression [↵](#9-expressions-)

```
<index-expr> := <expr> [ '?' ] '[' [ '^' ] [ '?' ] <expr> { ',' <expr> }* [ ',' ] ']'
```

An index expression can be used to get a value out of a type using a given index.

An index expression may also indicate that it uses reverse indexing, meaning that the index is an offset from the end by using the `^` character.

In addition to direct indexing, there is also a variant allowing for the index expression to return an optional value that will be `.None` when no value is found at the given index.
This is done by using the `[?expr]` version of the indexing expression.

Similar to a [field access expression](#916-field-access-), tuple index expressions also support `?.` (null-propagating) accesses.
The indexing will only happen if the value it is called on is a non 'err' value, otherwise it will propagate this value.
The `?.` access is supported on any type that implements the `OptAccess` trait.

When the expression being indexed is either an array or a slice, it will get the relevant element at a given index or a subslice at the given range.
If the array of slice is mutable, the resulting value will be memory location that can be assigned to.

Indices are 0-based for arrays and slices.
If array access is a constant expression and the array has a known size, bounds can be checked at compile-time, otherwise, if the relevant safety check is enabled, the check will be performed at runtime and will panic when being indexed out of range.

For any other type, the resulting indexing will depend on whether the index implementation returns a reference or not.

Multiple index expression can be provided, these will implicitly get converted to a tuple expression when calling the relavent indexing method.
When a multi-dimenional array or slice (including mixed arrays and slices) is used, the comma separated list can be used to index into all of them using a single expression, meaning `a[b][c]` can be written as `a[b,c]`.

For all other types, the following operations will happen:
- In an immutable place context, the resulting value will be `Index::index(&a, b)` or `OptIndex::opt_index(&a, b)`.

  If the index implementation were to return a reference, it will be implicitly dereferenced, this can be avoided by taking a reference to the returned element.

- In a mutable place context, the resulting value will be `*IndexMut::index_mut(&a, b)` or `OptIndexMut::opt_index_mut(&a, b)`.

Indexing allows comma expressions to be passed, this will implicitly be converted to an index call taking in a tuple of expressions.

The interfaces associated with the index expressions are:
- `Index`
- `IndexMut`
- `OptIndex`
- `OptIndexMut`
- `RevIndex`
- `RevIndexMut`
- `RevOptIndex`
- `RevOptIndexMut`

> _Todo_: Can we unify this a bit more?

## 9.12. Tuple index expression [↵](#9-expressions-)

```
<tuple-index-expr> := <expr> [ '?' ] '.' <int-decimal-literal>
```

A tuple index expressions is used to access fields within a tuple type (a tuple or tuple structure).

A tuple is indexed using an unsigned decimal literal, with no leading zeros or underscores.

Evaluation of a tuple has no side-effects, other than the evaluation of the tuple operand.
This expressions is a place expression, so it evaluates to the location of tuple field with the same name as the tuple index.

Similar to a [field access expression](#916-field-access-), tuple index expressions also support `?.` (null-propagating) accesses.
The indexing will only happen if the value it is called on is a non 'err' value, otherwise it will propagate this value.
The `?.` access is supported on any type that implements the `OptAccess` trait.


> _Note_: Named tuples are indexed using a [field access expression](#916-field-access-)

## 9.13. Call expression [↵](#9-expressions-)

```
<func-call> := <expr> <function-args>
<func-args> := [ '?' ] '(' <func-arg> { ',' <func-arg> }* [ ',' ] ')' [ <trailing-closure> ]
             | <trailing-closure>
<func-args> := [ <name> ':' ] <expr>
```

A call expessions calls a function.

The expression will complete when the function returns.
If the function return a value, this value will be returned, this function is therefore a place or value expression, depending on the returned value.

The function expression can be called if it follows either of the following cases
- The expression is of a function or function pointer type.
- The expression is of a value that implement one of the relevant function interfaces, being:
    - `Fn`
    - `FnMut`
    - `FnOnce`

If needed, an automatic borrow of the function expression is taken.

If a value passed to an argument matches the is a name matching that of the label, the label may be left out.

An argument can have an additional function argument label in case the function requires one.
Any default arguments do not need to be provided and will be evaluated after evaluating the supplied operands, in the order they were defined in the signature.

Arguments are evaluated in the order they are written. i.e. left-to-right.

Whenever the function arguments have a `?` between the name and them, this means that this is a call of an optional trait function, which only calls the function when it is not null, or otherwise return a `.None` value.
When the function is called, it will automatically be wrapped in a nullable type.
In a case where the function returns a nullable type by itself, it will not be wrapped, but instead will just propage the `.None` value.

> _Todo_: Check if the call interfaces are correct

> _Todo_: Can labeled arguments be put out of order?

### 9.13.1. Calling methods as functions [↵](#913-call-expression-)

All methods can be called as if they were functions.
The reciever may be passed as:
- the first arguments
- an arguments with the `self` label

Using the `self` label to call the function is not required, and the value may be passed unlabeled.

This can be useful for avoiding the receiver's automatic dereferencing, for example when using smart pointer types.

Example:
```
struct Foo {
    fn(&self) bar();
}

foo := Foo{};

foo.bar();
// can also be called as
Foo.bar(foo);
```

### 9.13.2. Trailing closure [↵](#913-call-expression-)
```
<trailing-closures> := <trailing-closure> { <name> ':' <trailing-closure> }*
<trailing-closure> := '{' [ [ <capture-list> ] [ <closure-params> ] [ '->' <type> ] '=>' ] { <stmt> }* <expr> '}'
```

A trailing closure expression is a special version of a closure syntax that allows the closure to be defined behind a function.
For a trailing closure to be allowed, the function being called must take closures as its final arguments.

If a function contains multiple trailing closure arguments, the call may have multiple trailing closures, where the first closure will be matched to the first closure parameter that is compatible with it.
Any closure before the matched closure can then not be assigned via a trailing closure.
Any closures after the matched closure may be passed with an argument label, followed by another trailing closure.
Unless the closures after the matching closure have a default value, they are required to have a value passed to them.

For example:
```
fn foo(a: ?fn() -> i32 = None, b: ?fn(i32) -> i32 = None, c: ?fn(i32) -> i32 = None);

foo { $0 } c: { $0 }; // Calls `foo(b: fn{$0}, c: fn{$0})`
foo { $0 } // Calls `foo(b: fn{$0})`
foo { 1 } // Calls `foo(a: fn{1});


fn bar(a: ?fn() -> i32 = None, b: ?fn(i32) -> i32 = None, c: ?fn(i32) -> i32);
// The following calls all cause errors:
bar { $0 }; // trailing closure passed to `b`, cannot find trailing closure for `c`
bar { 1 }; // Trailing closure passed to `a`, cannot find trailing closure for `c`
bar { 1 } { $0 }; // Found block after trailing closure, additional trailing closures need to marked using their labels
```


If the function only has closure arguments passed to it, the parentheses may be left out, meaning that:
```
fn foo<T>(by: T) where T: Fn(i32, i32) -> bool { .. }
```
may be called as
```
foo() { $0 > $1 }
```
or as
```
foo { $0 > $1 }
```
> _Note_: This feature is also used by [initializers](#716-initializers-)

## 9.14. Method call expression [↵](#9-expressions-)

```
<method-call-expr> := <expr> '.' <iden> <func-args>
```

Method calls are used to call any associated method, either as direct methods or trait method implementations.
They are done by statically dispacthing to a method if the exact slef-type of the left-hand side is known, or dynamically dispatching if the left-hand side expression is a trait object.

Each argument can have an additional function argument label in case the function requires one.
Any default arguments do not need to be provided and will be evaluated after evaluating the supplied operands, in the order they were defined in the signature.

Like function calls, the method calls can call optional trait methods.

### 9.14.1. Method lookup [↵](#914-method-call-expression-)

When looking up a method call, the receiver may be automatically dereferenced or borrowed in order to call a method.
This requires a more complex lookup process than for other functions, since there may be a number of possible methods to call. The following procedure is used:

1. Build a list of candidate receiver types.
   1. Obtained by repeatedly dereferencing the receiver's type, adding each type encountered to the list.
   2. Finally attempt an unsized coercion at the end, and adding the result type to the candidate list if that is successful.
   3. Then for each candidate `T`, add `&T` and `&mut T` to the list immediately after `T`.
2. Then for each candidate type `T`, search for a visible method with a receiver of that type, and arguments matching the provided labels, in the following places.
   1. `T`'s inherent methods (methods implemented directly by T).
   2. Any of the methods provided by a visible interface implemented by `T`.
      If `T` is a type parameter, methods provided by interface bounds on `T` are looked up first.
   3. All remaining methods scopes are looked up.
3. Pick the methods matching the arguments.

> _Note_: more detailed info about argument resolution and conflicts, check the [function definition item](#73-function-)

If this results in multiple possible candidates, it is an error, and the receiver must be converted to an appropriate receiver type to make the method call.

This process does not take into account the mutability of the receiver, or whether a method is `unsafe`.
Once a method is looked up, if it can't be called for one (or more) of those reasons, it will result in a compiler error.

If a step is reached where there is more than one possible methods such as where generic methods or interfaces are considered the same, then it is a compiler error.
These cases require a disambiguating as the methods identifier.

## 9.15. Field access [↵](#9-expressions-)

```
<field-access-iden> := <expr-path-ident> | <path-disambig>
<field-access-expr> := <expr> ( '.' | '?.' ) <field-access-iden>
```

A field expression is a place expression that evaluates to the location of a field of a struct or union.
When the operand is mutable, the field expression is also mutable.

If the `?.` access is uses, the field will only be accessed if the value it is called on is a non 'err' value, otherwise it will propagate this value.
The `?.` access is supported on any type that implements the `OptAccess` trait.

If a field expression is followed by an opening parenthesis, this would interpreted as a method call expression.

### 9.15.1 Automatic dereferencing [↵](#915-field-access-)

If the type of the left-hand-side operand implements `Deref` or `DerefMut` depending on whether the operand is mutable, it is automatically dereferenced as many times as necessary to make the field access possible.
This process is also called 'autoderef' for short.

### 9.15.2. Borrowing [↵](#915-field-access-)

The field of a struct or a reference to a struct are treated as separate entities when borrowing.
If the struct does not implement `Drop` and is stored in a local variable, this also applies to moving out of each of its fields.
This also does not apply if automatic dereferencing is done through user defined types that don't support this.

## 9.16. Closure expressions [↵](#9-expressions-)
```
<closure-expr>            := [ 'unsafe' ] [ 'async' ] 'fn' '{' [ [ <capture-list> ] [ <closure-params> ] [ '->' <type> ] '=>' ] { <stmt> }* <expr> '}'

<closure-params>          := <simple-closure-params> | <full-closure-params>
<simple-closure-params>   := <name> { ',' <name> } [ ',' ]
<full-closure-params>     := <capture-list> '(' <full-closure-param> { ',' <full-closure-param> } [ ',' ] ')'
<full-closure-param>      := { <attribute> }* <pattern-no-top-alt> [ ':' <type> ]

<closure-list>            := '[' <closure-capture> { ',' <closure-capture> }* [ ',' ] ']'
<clusure-capture>         := <closure-common-capture> | <closure-var-capture>
<closure-common-capture>  := 'move' | ( '&' [ 'mut' ] )
<closure-var-capture>     := [  ( '&' [ 'unique' | 'mut' ] ) ] <name> [ '=' <path> ]

```
A closure expression, also known as a lambda or a functor in some langauges, denotes a function that maps a list of parameters onto an expression that follows them.
In additon, closure can capture values from the surrounding context and use them within the closure, this differentiates them from functions.

There are 2 ways of writing parameters for a closure:
- a simple list of names, which are untyped and cannot have any patterns
- a full list of parameters, which can: be typed, contain patterns, and have attributes applied to the parameters

When patterns are used for parameters, they are just like `let` bindings, meaning that they must be irrefutable patterns.

Each closure expression has a unique, anonymous type.

The main difference between functions and closures, is that closures may capture variables from the environment around it.

How variables are captures depends on which style of parameters is used, meaning that if a full list of parameters is names, a capture list may also be provided.
If no explicit captures are provided, or a captured variable is not included within the capture list, the captures variable follows the default capture behavior as defined in the [closure type capture modes](#capture-modes-).

Captures can be expressed per variable, or as a common capture mode.
Common capture modes override the default capture behavior and makes it so that all non-explicitly captured variables follow the mode defined by it.
In addition, variable specific captures may also be defined, allow the exact capture mode for a variable to be defined.

A capture can also be a path-assigned capture, allowing a path to be refered to as a name within the closure.

If all variables are captured by move, this means that the closure is independent from all lifetimes of those variables and can be used when the variables would normally cause the closure to be invalid.

Closures may also be marked as `unsafe`, this means that their body is an unsafe context, but does not make the closure itself `unsafe` to do operation on.
This is equivalent to `fn { unsafe { ... } }`

Closures can also be marked as `async`, this is similar to [`async` blocks](#953-async-blocks-), but allowing arguments to be passed.

### 9.16.1. Shorthand arguments [↵](#916-closure-expressions-)

A shorthand arguments allow a closure to be written without explicilty listing any variables, by simple leaving them out.
This also allows the programmer to directly write the closure as if it were a block.

Arguments can still be accessed as `$N`, where `N` represent the 'index' of the parameter.

### 9.16.2. Closure trait implementations [↵](#916-closure-expressions-)

Which trait a closure type implements depends on how variables are captured and the types of the captured expression.
See the call trait section for how and when a closure implements the respective trait.

### 9.16.3. Operator methods [↵](#916-closure-expressions-)
```
<op-method-expr> := <operator> [ '$' ]
                  | '$' <operator>
```

Any location that accepts a closure can also accepts so-called operator methods, allowing an operator to be passed.
Infix operators can be passed as-is, this is the default way these operators will be interpreted.
Pre- and postfix operators must be identified using a special `$` character, either before or after the operator, to indicate which type of operator should be used.

### 9.16.5. Autoclosures [↵](#916-closure-expressions-)

Autoclosures allow regular expressions to be passed to any parameter accepting a closure, which has the parameter marked as `@autoclosure`, otherwise the closure needs to be explicitly provided.
They will automatically be converted into a closure.
This is only allowed when the expression does not have any parameters.

> _Note_: This is essentially how [lazy parameters](#lazy-parameters) are passed to a function

### 9.16.4. Closure argument expressions [↵](#916-closure-expressions-)
```
<closure-arg-expr> := '$' <int-dec-literal>
```

A closure argument expression is used to access unnamed closure arguments.

## 9.17. Full range expression [↵](#9-expressions-)

```
<full-range-expr> := '..'
```

The `..` expression, unlike the range operators, represents an unbounded range, with beginning or ending.
One of the usecases of this expression is to convert something into a slice by indexing using a full range.

## 9.18. If expression [↵](#9-expressions-)

```
<if-expr>          := <label> [ 'const' ] 'if' <branch-condition> <block> ( 'else' <block> )?
                    | <err-if-expr>
<branch-condition> := ( <expr> | <cond-let-binding> ) ( '&&' ( <expr> | <cond-let-binding> ) )*
```

An `if` expression is a conditional branch in program control.

An if expression will execute its subsequent block when the condition is `true`, and will skip all other blocks.
Otherwise when an `else` is present, it will be executed.
This happens for each if statement if they are chained, until a block is executed.

By default, all if statements evaluate to a value of `()`.
When any branch returns a value, all possible branches should return the value a value with the same type.

An if-statement can be explicilty marked as `const`, guaranteeing it will execute at compile time, which essentially eliminates the if-statement.
If just a constant expressions is supplied, the compiler may eliminate the if, but there is no guarantee it will.

### 9.18.1. Branch conditions & `let` bindings [↵](#918-if-expression-)
```
<branch-condition> := <condition-elem> { '&&' <condition-elem> }*
<condition-elem>   := <expr> | <opt-shorthand> | <let-binding>
```

A branch condition is any expression that evaluates to a boolean expression.

Both the `&&` and `||` operator have special meaning in a condition, depending on the condition itself.
If any `let` binding or optional shorthand is used, they have the following properties:
- `&&`: any subsequent condition has access to the newly created values generated
- `||`: Any outer `&&` does not propagate the bound values within either side of the `||`, expect when each sub-condition has a bound value with the same name and type as in any other sub-condition.

#### Optional shorthand [↵](#9181-branch-conditions--let-bindings-)
```
<opt-shorthand> := [ <name> '=' ] <expr> '?'
```

Within a condition, any use of the `?` operator on an optional in a top-level expression is syntactic sugar for `let Some(name) = name`, implicitly upgrading the optional value to a non-optional value that can be used.
If the expression is not a name, it can be bound by assigning it to a variable.

#### Let bindings [↵](#9181-branch-conditions--let-bindings-)
```
<let-binding> := 'let' <pattern> '=' ? <scrutinee> excluding lazy boolean operator expressions ?
```

Within a condition, a special `let` binding can appear, this allows for pattern matching inside of the condition.
The `let` binding evaluates to a true value when the scrutinee matches the provided pattern.

If a `let` binding binds any values, these values may then be used anywhere in the conditional chain that is following it, when it is separated by `&&`.

Multiple pattern may be specified using the `|` operator, and has similar semantics as `|` in  [`match` expression](#920-match-expression-).
This includes that to use any bound values later on in the chain, it needs to be present in all sub-patterns, with the same bind mode and type.

### 9.18.2. Result if expressions [↵](#918-if-expression-)
```
<res-if-expr> := 'if' [ <name> '=' ] <expr> '!' <block> 'else' '(' <name> ')' <block>
```

A result if expression is a special variant of an if expression that allows you to handle result types without requiring a `match` expression.
It is syntactic sugar, allowing a value to be checked, and handled accordingly.
If the result is a valid value, the name of the result value can either be directly used, or if the expression is not a name, be bound via an assignment.
The else case is then also provided with a named variable representing the error within the result, this will be moved into the bound variable name.

> _Note_: This is equivalent to
> ```
> match <expr> {
>     Ok(value) => { ... },
>     Err(err)  => { ... },
> }
> ```

## 9.19. Loops [↵](#9-expressions-)
```
<loop> := <loop-expr>
        | <while-expr>
        | <res-while-expr>
        | <do-while-expr>
        | <for-expr>
        | <labelled-block-expr>
```

there is support for five loop expressions:
- a `loop` expression denoting an infinite loop
- a `while` expression looping until a predicate is false
- a `do while` expression looping until a predicate is false, guaranteeing to run the loop at least once.
- a `for` expression extracting a value from an iterator, looping until the iterator is empty
- a labelled block expression running a loop exactly once, but allowing the loop to exit early with `break`

All five types of loop expression support `break` expressions and labels.
All except labelled block expressions support `continue` expressions.

The condition of a loop, or the source of a for loop may not contain a struct expression without being wrapped in a block or parentheses.

In the following locations in loops may not contain a struct expression, without being wrapped in a block or parentheses, or in a let binding.
- a `while` condition
- a `for` source

> _Note_: In the context of loops, a non-trivial value means any value that is either of type `()` or `!`

### 9.19.1. Loop expression [↵](#919-loops-)

> _Todo_: Fix up if never type is changed

```
<loop-expr> := <label> 'loop' <block>
```

A `loop` expression repeats execution of a body continuously.

A `loop` expression without an associated `break` expression is diverging and has type `!`.
A loop expression containing an associated `break` expressions can terminate, and must be type compatible with the value of the other `break` expressions.

### 9.19.2. While expression [↵](#919-loops-)

```
<while-expr> := <label> 'while' <branch-condition> [ ';' <expr> ] <basic-block> [ 'else' <block> ]
```

A `while` loop begins by evaluating the loop condition operand.
If the loop conditional operand evaluates to `true`, the loop block executes, the control return to the loop conditional operand.
If the loop conditional expression evaluates to `false`, the `while` expression completes.

A while loop can also have an increment expression, this is followed after the branch condition, and separated by a ';'.
This expression will be called at the end of each loop.
The increment can be used emulated C-style for-loops.

#### While else [↵](#9192-while-expression-)

`while` loops have support for an else statement, these can be used for 2 distinct reasons:
- if a `while` contains a `break` with a non-trivial value, an else statement is required to be added, and will be executed if no `break` is hit.
  It must therefore return a value with the same type as the type of the value provided to the `break`.
- if a `while` contains no `break` or one that only returns a trivial value, the else statement will only be executed if the first iteration is not entered.
  Meaning that this only will happen when the initial condition returns `false`, not when the loop break on a subsequent iteration.

#### Result while [↵](#9192-while-expression-)
```
<res-while-expr> := <label> 'while' [ <name> '=' ] <expr> '!' <block> 'else' '(' <name> ')' <block>
```

Similar to a result `if`, a result `while` is syntactic sugar for handling result types within a while loop, without needing to have a nested `match`.
If the result is a valid value, the while loop is entered and the value can either be directly used, or if the expression is not a name, be bound via an assignment.
If any iteration of the loop result in an error, the else block will be executed, and the error will be moved into the bound variable name.


> A result while is equivalent to
> ```
> :result_while: loop {
>     match <expr> {
>         Ok(value) => { ... }
>         Err(err) => { ...; break :result_while:; }
>     }
> }
> ```

## 9.19.3. Do-while expression [↵](#919-loops-)

```
<do-while-expr> := <label> 'do' <block> 'while' <expr> [ 'else' <block> ]
```

A `do while` loops begins by running the body of the loop at least once, after which the boolean loop condition operand is evaluated.
If the loop conditional operand evaluates to `true`, the loop block executes, the control return to the loop conditional operand.
If the loop conditional expression evaluates to `false`, the `do while` expression completes.

#### Do while else [↵](#9193-do-while-expression-)

`do while` loops have support for an else block whenever the loop contains a `break` with a non-trivial value.
Then an else statement is required to be added, and will be executed if no `break` is hit.
It must therefore return a value with the same type as the type of the value provided to the `break`.

### 9.19.4. For expression [↵](#919-loops-)

```
<for-expr> := [ <label> ] 'for' <pattern> 'in' <expr> <block> [ 'else' <block> ]
```

A `for` expression is a syntactic construct for looping over elements provided by an implementation of `IntoIterator`.
If the iterator yields a value, that value is matched against the irrefutable pattern, the body of the loop is executed, and then control returns to the head of the `for` loop.
If the iterator is empty, the `for` expression completes.

#### For else [↵](#9194-for-expression-)

`for` loops have support for an else statement, these can be used for 2 distinct reasons:
- if a `for` contains a `break` with a non-trivial value, an else statement is required to be added, and will be executed if no `break` is hit.
  It must therefore return a value with the same type as the type of the value provided to the `break`.
- if a `for` contains no `break` or one that only returns a trivial value, the else statement will only be executed if the first iteration is not entered.
  Meaning that this only will happen when the initial condition returns `false`, not when the loop break on a subsequent iteration.

#### Result for [↵](#9194-for-expression-)
```
<res-for-expr> := [ <label> ] 'for' <pattern> '!' 'in <expr> <block> 'else' '(' <name> ')' <block>
```

Similar to a result `if`, the result `for` is syntactic sugar for handlign resutl types whitin a for loop, without needing to have a nested `match`.
If the result of an iteration is a valid value, the for loop is entered and the values bound in the pattern can either be directly used.
If any iteration of the loop result in an error, the else block will be executed, and the error will be moved into the bound variable name.

### 9.19.5. Labelled block expressions [↵](#919-loops-)

```
<labelled-block-expr> := <label> <block-expr>
```

Labelled block expressions are exactly like block expressions, except they allow using `break` expressions within the block.
Unlike loops, `break` expressions within a labelled block experssion must have a label (i.e. the label is not optional).
Similarly, labelled block expressions must begin with a label.

### 9.19.6. Loop labels [↵](#919-loops-)

```
<label> := ':' <name> ':'
```

A loop expression may optionally have a label.
If the label is present, the labeled `break` and `continue` expressions nested within the loop may exit out of this loop or return control to its head.

Labels follow the hygeine and shadowing rules of local variables.

## 9.20. Match expression [↵](#9-expressions-)

```
<match-expr> := [ <label> ] `match` <expr> '{' { <match-case> }* <final-case> '}'
<match-case> := [ <label> ] <pattern> '=>' ( <expr> ',' | <block> [ ',' ] )
<final-case> := [ <label> ] <pattern> '=>' ( <expr> [ ',' ] | <block> [ ',' ] )
<scrutinee>  := ? <expr> except <struct-expr> ?
```

A `match` expression branches based on a value of a scrutinee.
The value of which will then be checked compared to the patterns provided within each branch.
The scrutinee and all expression must have the same type.

A `match` behaves differently depending on whether or not the scrutinee expression is a place or value expression.
Place expression will immediatelly be matched, while value expressions will first be evaluated to a temporary location.
This value is subsequently compared to the patterns in the arms until a match is found.
The first arm with a matching pattern is chosen as the branch target of the `match`, any variables bound by the pattern are assigned to local variables in the arm's block, and control enters the block.

When the scrutinee is a place expression, the match does not allocate a temporary location; however, a by-value binding may copy or move from the memory location.
When possible, it is preferable to match on place expressions, as the lifetime of these matches inherits the lifetime of the place expression rather than being restricted to the inside of the match.

Variables bound within the pattern are scoped to the match guard and the arm's expression.
The binding mode (move, copy, or reference) depends on the pattern.

Multiple match patterns may be joined with the `|` operator.
Each pattern will be tested in a left-to-right sequence until a successful match is found

Every binding in each `|` separated pattern must appear in all of the patterns in the arm.
Every binding of the same name must have the same type, and have the same binding mode.

### 9.20.1. Fallthrough labels [↵](#920-match-expression-)

A pattern is allowed to have a label.
A label may only be referenced by a `fallthrough` expression within an arm of the `match` expression.
This will then proceed to evaluate another arm in the `match`.

Labels are only allowed if the arm does not capture any bindings.

## 9.21. Break expression [↵](#9-expressions-)

```
<break-expr> := 'break' [ <label> ] [ <expr> ]
```

When `break` is encountered:
- in a loop, execution of the associated loop body is immediatelly terminated.
- in a `match`, execution of the associated arm is immediatelly terminated.

A `break` expression is normaly associated with the innermost loop or `match` exclosing the `break` expression, but a label can be used to specify which enclosing loop or `match` is affected.

A `break` expression is only permited in the body of a loop, or an arm of a `match`.

### 9.21.1. Break and loop/match values [↵](#921-break-expression-)

When associated with a loop, a break expression may additionally return a value from that loop.
`break` without an explicit expression is considered identical to a `break` with the expression `()`.

## 9.22. Continue expression [↵](#9-expressions-)

```
<continue-expr> := 'continue' [ <label> ]
```

When `continue` is encountered, the current iteration of the associated loop body is immediatally terminated, returning control to the loop head.
These correspond to the following for given loops:
- `loop`: the head is the the start of the loop body
- `while` loop: the head is the increment (if it exists), or the conditional expression controllering the loop (which also always follows the increment).
- `do while` loop: the head is the conditional expression controlling the loop
- `for` loop: the head is the call expression controlling the loop
In addition, when used withing a `match`, it may also re-evaluate the `match` expression, an example of this would be a state machine.

Like a `break`, `continue` is normally associated with the innermost enclosing loop, but `continue 'label` may be used to specify the loop affected.
A `continue` expression is only permitted in the body of a loop.

> _Note_: The benefit of using `continue` in a `match` over using a loop, is that it allows a jump to be inlined at the tail of a branch, which may allow for better branch prediction.

## 9.23. Fallthrough expression [↵](#9-expressions-)

```
<fallthrough-expr> := 'fallthrough' [ <label> ]
```

When a `fallthrough` is encountered, the current arm of a `match` will immediatelly terminate and the arm next arm will be evaluated.
If a label is given, the arm associated with the label will be evaluated instead.

## 9.24. Return expression [↵](#9-expressions-)

```
<return-expr> := 'return' [ <expr> ]
```

Return expressions moves its argument into the designated output location for the current function call, destroys the current function activation frame, and transfers control to the caller frame.
When the function being called has named returns, the `return` expression is allowed to overwrite the named return values.

If a return is followed by a comma expression, it will return a tuple, which may be named.

### 9.24.1. Return in generator functions [↵](#924-return-expression-)

In addition to the regular use of a return, it also has a special meaning in a generator function.
Unlike a [`yield` expression](#934-yield-expressions-), which allows the async function to generate more values, `return` fully terminates the function.

## 9.25. Underscore expression [↵](#9-expressions-)

```
<underscore-expr> := '_'
```

Underscore expressions are used to signify a placeholder, they may be used in the following locations:
- as a placeholder in a destructuring assignment
- at the left hand side of an expression, causing it to use, but immediatelly discard the value.
- as an inferred arguments for type parameters. This requires the type to be inferrable from the context around the expression

> _Note_: that this is distinct from a wildcard pattern.

## 9.62. Throw expression [↵](#9-expressions-)

```
<throw-expr> := `throw` <expr>
```

Throw can be used to return an erronous value from a function returning an erronous-supporting type, and also evaluate all in-scope [defer-on-error statements](#841-defer-on-error-statement-).

Unlike languages with exception, this expression can be seen as a 'fancy' return expression returning an erronous value, thus _not_ causing any unclear control flow.

## 9.27. Try expression [↵](#9-expressions-)
```
<try-expr> := 'try' [ '?' | '!' ] <expr>
```

The try expression allows the code to handle both optional and result values in 3 ways:
- `try`: the propagating try, if the value is a `None` or `Err()`, the code will [throw the error as defined above](#926-throw-expression-) 
- `try!`: the unwrapping try, if the value is a `None` or `Err()`, the code will panic

These operators have the following operator traits associated with them:
- `try`: `Try` 
- `try!`: `TryUnwrap`

When a try expression is followed by a block, it's a so-called try-block.
In a try-block, the try applied to the block will be applied on every call that supports the correspondin try, unless they are explicitly handled.

Each try has their corresponding post-fix operator version which can be nested within other expressions.
These operators are the [try operators](#1423-try-operator-)

## 9.28. Catch expression [↵](#9-expressions-)
```
<catch-expr> := <expr> 'catch' '(' <name> ')' <expr>
```

A catch expression calculate the value proceeding it first, and if the returning value would be a valid value, that value is returned.
If the returning value is an erronous value, the catch' body will be executed, which captures the error value an can be used by the expression following it

To decide wether the the left-hand value is an 'erronous' value, the `Catch` trait is used

This is shorthand for:
```
if val = (expr).(Catch.check)()! {
    val
} else (err) {
    // ...
};
```

For a non-error capturing equivalent, use the corresponding [`??` operator](#1437-err-coalescing-operator-)

## 9.29. Comma expression [↵](#9-expressions-)

```
<comma-expr> := <comma-elem> { ',' <comma-elem> }*
<comma-elem> := [ <name> ':' ] <expr>
```

Comma expressions are a set of expressions separated by commas.
It is a very niche expression type that has a very limited amount of places it can be used.

Comma expression may also includ names for each element, an example usecase of this is when returning a comma expression, where the names can be used to label the values for a named tuple.

## 9.30. When expression [↵](#9-expressions-)

```
<when-expression> := 'when' <expr> <block> [ 'else' <expr-with-block> ]
```

A `when` expression is similar to an if expressions, but comes with 2 fundamental differences
- The condition needs to be compile time expression
- The when expression does not produce a new scope, instead the content will be placed in the surrounding scope.

The `else` can only be followed by a block, or another `when` expression.

This can be thought of as containing code marked with the cfg attribute.

## 9.31. Template string expressions [↵](#9-expressions-)

```
<template-string-epxr> := ( '$' | <name> ) <string-literal>
```

A template string expressions can be seen as a special function call to a template string function, but works by directly prepending the name of it in front of the string literal.
Template string allow for interpolations to be added in the string passed to them.

In addition to interpolation, it is also common practice that in cases where the template string function is called like a normal function, to allow value to be passed as positionals, using `{N}`, where `N` represents the index within the variadic arguments passed to said function.
Although this practice may differ between different functions.

A special template string exists which is prefixed by `$`, this calls the format string template as defined in the implicit context, allowing for a convenient shortcut to format a string.

The functions that are used for template string functions are defined [here](#7311-string-template-functions-)

## 9.32 Key-path expressions [↵](#9-expressions-)
```
<key-path>           := '\' <path> '.' <key-path-member> { [ '?' | '!' ] <key-path-subscript> }*
                      | '\' <path> '.' 'self'
<key-path-member>    := <name>
                      | <int-dec-literal>
                      | '[' <expr> ']'
<key-path-subscript> := '.' <name>
                      | '.' <int-dec-literal>
                      | '[' <expr> ']'
```

A key-path represents a path that points to a member of a type, as either a named member, or a tuple index.
A key-path is a compile-time value of the builtin `KeyPath(Ty, Member)` type.
If the type the path is on can be inferred, the path may be an inferred path.

Multiple keypaths may be chained by a sequence of accesses, for example, the keypath `\Foo.bar.baz` refers to the inner `baz` value of `Foo` in the following code:
```
struct Foo {
    bar: struct {
        baz: i32 // <-
    }
}
```

This sequence of accesses may be any of the following:
- field access
- tuple access
- index

In addition to direct access, the following are also supported:
- `?`: Handles optional chaining within the path
- `!`: Unwraps the preceeding value before an access

The keypath may also refer to the value itself, this is done using a special `self` key-path.

Any expression in a keypath that cannot be collapsed to a compile-time value will only be exectuded when using the specific keypath, not when the keypath is created, meaning it captures whatever value it needs to execute.


By default, each type implements an index by key-path on itself, returning a reference to an `Any` value.

A key-path can also be implicitly converted to an autoclosure, where it accessed its element from its root type, i.e. `fn{ (value: Ty) => value.Member }`.

An example usecase of a key-path could be to notify a value change on a property to a generic listener.

## 9.33. Await expressions [↵](#9-expressions-)
```
<await-expr> := `await` <expr>
```

An await expression allows an async function to wait for the completion of another async function.

Await expression are only legal in an async context.

> _Todo_: The exact interaction and how it works still needs to be figure out

## 9.34. Yield expressions [↵](#9-expressions-)
```
<yield-expr> := `yield` [ <expr> ]
```

A yield expression allows a generator function to return a value, while still allowing it to produce more values after it.

A function can use any number of `yield`s to return values.

There is a different between a `yield` in a regular and an `async` context:
- when in a regular generator function, a yield is required to have a value.
- when in an `async` generator function, the return value is optional. If no value is provided, the `yield` will just suspend the async function.

# 10. Patterns [↵](#tables-of-contents)

```
<pattern> := <pattern-no-top-alt> ( | <pattern-no-top-alt> )*
<pattern-no-top-alt> := <pattern-no-range>
                      | <range-pattern>
<pattern-no-range> := <lit-pattern>
                    | <identifier-pattern>
                    | <wildcard-pattern>
                    | <reference-pattern>
                    | <struct-pattern>
                    | <tuple-struct-pattern>


```

Patterns are both used to match values, but also to optionally bind them.
They are used in the following:
- [variable declarations](#82-variable-declaration-) using `let`
- [Function](#731-parameters-) and [closure](#916-closure-expressions-) parameters
- [`match` expressions](#920-match-expression-)
- [conditional `let` bindings](#let-bindings-)
- [`for` loops](#9194-for-expression-)

Patterns can be used to destructure types like struct, enums, and tuples.
Destructuring breaks up a value in its constituent elements.

Patterns can be said to be refutable if there is a possibility for it to not be matched, otherwise if they will always be matched, they are said to be irrifutable.

## 10.1. Literal pattern [↵](#10-patterns-)

```
<lit-pattern> := [ '-' ] <literal-expr>
```

Literal patterns match the exact value of the literal.
Since literals cannot contain a `-` sign, the literal pattern adds support for it.

This pattern is always refutable.

## 10.2. Identifier pattern [↵](#10-patterns-)

```
<identifier-pattern> := [ 'ref' ] [ 'mut' ] <name> [ '@' <pattern-no-top-alt> ]
```

Identifier patterns bind the value they are matched to to a variable of a given name.
This names needs to be unique within the pattern.
This binding (newly created variable) is allowed to shadow any variable that is defined before the pattern.
The scope of the binding depends on the location of where the pattern is used.

`mut` can be added to make the resulting binding mutable in code, if this is used with ut `ref`, the value will be moved into the binding.
`ref` can be added to take reference to the element being matched, instead of moving or copying it on match.
`ref` must be used instead of '&' as it is used to destructure a reference, i.e. it removes the reference from the variable.

A binding may be restricted to only bind if the value adheres to another pattern, which is located behind the name.
An identifier pattern is irrifutable if the subpattern is irrifutable, or no subpattern is provided.

By default, the variable will be copied or moved, depending on wether a value implements `Copy`.

### 10.2.1. Binding modes [↵](#102-identifier-pattern-)

For ergonomics reasons, identifier patterns can operate in different binding modes depending on the context they are found in.
The binding mode is dependent on the value being matched, the pattern, and the explicitly defined binding mode.

When matching a pattern, the value being matched and the pattern are used to determine the binding mode, as defined [below](#determining-the-default-binding-move-).
To avoid the default binding mode being something other than 'move', the value being matched against can either be derefenced before matching, or have the outer pattern being a [reference pattern](#106-reference-pattern-).

If no explicit `mut`, `ref`, or `ref mut` is defined, the binding uses the default mode.

The binding mode can only be explicitly set if the default binding mode is 'move'.
For example, the following are not valid, as they all have a default binding mode of 'ref':
```
let [mut a] = &[()]; // error
let [ref a] = &[()]; // error
let [ref mut a] = &[()]; // error
```
The same counts for a [reference pattern](#106-reference-pattern-).
Meaning the following is also not allowed:
```
let [&a] = &[&()]; // error
```

The above example can be valid, by explicitly placing the outer slice pattern in a reference pattern, like:
```
let &[ref a] = &[()]; // This is now allowed
```

Move and reference pattern may be combined together in a single pattern.
This will result the value being partially moved, and thus cannot be used afterwards.
This only applies when the type is not copyable.

For example:
```
let Person{ name, ref age } = person;

// _ = person.name; // error: 'name' has been moved
_ = person.age; // still allowed, age has not been moved
```

#### Determining the default binding move [↵](#1021-binding-modes-)

The default mode starts by being set to 'move'.
The compiler will then go over each pattern, and follow it until it hits a binding.

To decide the current default mode, the following is done:
- If a reference value is matched to an outer non-reference, for every reference in the value:
  1. update the default binding mode
    - If the value is a mutable reference, and the default mode is 'move', update the default mode to 'mut ref'
    - If the value is a shared reference, and the default move is not 'ref', update the default mode to 'ref'
  2. dereference the value
- Otherwise, the default mode will be set to 'move'

In short, this means if the outer value being matched against contains a non-mutable `&`, the default move will be 'ref'.
If instead it does not contain a non-mutable `&` and only `&mut`, the default move will be 'ref mut'.
And otherwise it will be 'move'

> _Note_: A non-reference pattern is any pattern, with the exception of the following:
> - [bindings i.e. identifer patterns](#102-identifier-pattern-)
> - [wildcard pattern](#103-wildcard-pattern-)
> - [paths to constant items](#1012-path-pattern-) of a reference type
> - [reference patterns](#106-reference-pattern-)

## 10.3. Wildcard pattern [↵](#10-patterns-)

```
<wildcard-patter> := '_'
```

A wildcard pattern matches any single element in a pattern, and is used to ignore its value.
A wildcard does not copy, move, or borrow the value it matches.

## 10.4. Rest pattern [↵](#10-patterns-)

```
<rest-pattern> := '..'
```

A rest pattern is a variable length wildcard pattern that matches 0 or more elements, and can be used to ignore those elements.
A rest pattern does not copy, move, or borrow the values it matches.

A wildcard may only appear in the following patterns:
- [tuple patterns](#109-tuple-pattern-)
- [array patterns](#1011-slice-pattern-)
- as an identifier's subpattern in an [slice pattern](#1011-slice-pattern-)

A special case of the wildcard that matches 0 or more elements, and can be used to discard any elements that are not cared about in the match.


## 10.5. Range pattern [↵](#10-patterns-)

```
<range-pattern>              := <exclusive-range-pattern>
                              | <inclusive-range-pattern>
                              | <from-range-pattern>
                              | <to-range-pattern>
                              | <inclusive-to-range-pattern>
<exclusive-range-pattern>    := <range-pattern-bound> '..' <range-pattern-bound>
<inclusive-range-pattern>    := <range-pattern-bound> '..=' <range-pattern-bound>
<from-range-pattern>         := <range-pattern-bound> '..'
<to-range-pattern>           := '..' <range-pattern-bound>
<inclusive-to-range-pattern> := '..=' <range-pattern-bound>
<range-pattern-bound>        := <number-literal>
                              | <char-literal>
                              | <path-expr>
```

Range expressions match any scalar value within the range of the bounds.
They are represented by a range operator with a value on at least one side.

An _exclusive range_ pattern matches all values from the lower bound up to, but **not** including, the upper bound.

An i_nclusive range_ pattern matches all values from the lower bound up to, **and** including, the upper bound.

A _from range_ pattern matches all values which are greater than or equal to the lower bound.

A _to range_ pattern amtches all value that are smaller than the upper bound.

The _inclusive to range_ patten matches all values that are smaller than or equal to the upper bound.


A bound may be any of the following:
- A character, byte character, integer, or floating point literal
- A `-`, followed by a character, byte character, integer, or floating point literal
- A path

If a bound is written as a path, it must point to a constant value with a character, integer, or floating point primitive type.

The lower bound may not be greater than the upper bound.

The range pattern matching any value of the same type as either of its bounds.
The bounds must therefore both be of the same type.

When using floating point literal, they must not have a type of `NaN`.

Patterns of a fix width primitive type are irrifutable, if the range spans the entirety of all possible values, e.g. `0..=255` for a `u8`.
The following patterns are irrifutable:
- An inclusive range from an integers minimum to maximum value
- A range of values that prececisly contains all supported characters, which are the following:
  - `char7`: `'\x00'..='\x7F'`
  - `char8`: `'\x00'..='\xFF'`
  - `char16`: `'\u{0000}'..='\u{D7FF}'` and `'\u{E000}'..='\u{FFFF}'`
  - `char`, `char32` : `'\u{0000}'..='\u{D7FF}'` and `'\u{E000}'..='\u{10FFF}'`

Range expression may not be used as a top level pattern within a slice a pattern, i.e. `[1.., a]` is **not** allowed

> _Todo_ Allow user-defined types supporting ranges as bounds ??

## 10.6. Reference pattern [↵](#10-patterns-)

```
<reference-pattern> := '&' [ 'mut' ] <pattern-no-range>
```

Reference patterns is used to derefence pointers and references, and thus, borrowing them.

Matching a value of type `&T` to a pattern like `&val` is equivalent to first dereferencing it and 

By adding `mut`, it is possible to dereference any mutable reference or pointer.

Reference patterns are always irrifutable.

## 10.7. Struct pattern [↵](#10-patterns-)

```
<struct-pattern>             := ( <path> | '.' ) '{' [ ( <struct-pattern-elem> { ',' <struct-pattern-elem> }* [ ',' [ <rest-pattern> ] ] ) | <rest-pattern> ] '}'
<struct-pattern-elem>        := { <attribute> }* ( <struct-pattern-elem-tuple> | <struct-pattern-elem-member> | <struct-pattern-elem-iden> )
<struct-pattern-elem-tuple>  := <tuple-elem-idx> ':' <pattern>
<struct-pattern-elem-member> := <name> ':' pattern
<struct-pattern-elem-iden>   := [ 'ref' ] [ 'mut' ] <name> [ '@' <pattern> ]
<struct-pattern-etc>         := '..'
```

A struct pattern can match struct, enum, and union values that match the defined criteria in the subpatterns.
The also allow for the value to be deconstructed to its members.

Struct pattern can also have an inferred path by starting it with a '.'

There are 3 ways of matching elements:
- Using a tuple element in case of tuple-like types
- Using a values name, followed by a pattern
- Using a value directly with a matching name, which may also include a bound pattern.

A structure's field may be ignored by using a `..`, which is similar to a rest pattern, but is distinct in its meaning.
If a `..` is not used, the struct patterns is required to refernce all fields.

A struct pattern used to match a union must have exactly one field (see [pattern matching on unions](#pattern-matching-on-unions-))

When a field is directly used, it acts like a [identifier pattern](#102-identifier-pattern-).

A struct apttern is refutable if the pattern either refers to an variant or an enum with more than 1 variant, or if any of its subpatterns is refutable.

## 10.8. Tuple struct pattern [↵](#10-patterns-)

```
<tuple-struct-pattern>      := [ <path> | '.' ] '(' ( <tuple-pattern-elem> { ',' <tuple-pattern-elem> }* [ ',' ] ) | <rest-pattern> ')'
<tuple-struct-pattern-elem> := [ <name> ':' ] <pattern>
```

A tuple struct pattern can match tuple structs that match the defined criteria in the subpatterns.
They also allow for a tuple struct or enum value to be destructured into its fields.

Tuple struct pattern can also have an inferred path by starting it with a '.'

Only a single [rest pattern](#104-rest-pattern-) is allowed within a tuple struct pattern.

A tuple struct pattern is refutable if the pattern either refers to an variant or an enum with more than 1 variant, or if any of its subpatterns is refutable.

## 10.9. Tuple pattern [↵](#10-patterns-)

```
<tuple-pattern>      := '(' ( <tuple-pattern-elem> { ',' <tuple-pattern-elem> }* [ ',' ] ) | <rest-pattern> ')'
<tuple-pattern-elem> := [ <tuple-elem-idx> ':' ] <pattern>
```

A tuple pattern can match a tuple values that match the defined criteria in the subpatterns.
They also allow for a tuple to be destructured into its fields.

Only a single [rest pattern](#104-rest-pattern-) is allowed within a tuple pattern.

The form `(..)` is a special form of a tuple patterns, which matches a tuple of any size.
This patterns is always irrifutable.

A tuple with a single aptterns is a grouped pattern.

A tuple pattern is refutable if any of its fields are refutable.

## 10.10. Grouped pattern [↵](#10-patterns-)

```
<grouped-pattern> := '(' <pattern> ')'
```

Grouped patterns are used to explicitly control the precedence of compound patterns.

An example would be `&0..=5`, which is ambiguous and not allowed, this can be solved by instead writing `&(0..=5)`, as `&0` is not allowed in a range.

The form `(..)` is not a grouped pattern, but instead a [tuple pattern](#109-tuple-pattern-)

A gouped pattern is rufutable if it subpattern is refutable.

## 10.11. Slice pattern [↵](#10-patterns-)

```
<slice-patter> := '[' <pattern> { ',' <pattern> }* [ ',' ] ']'
```

A slice pattern can match array and slice values that match the defined criteria in the subpatterns.

The form `[..]` is a special form of a slice pattern, which matches a slice of any size.
This pattern is always irrufutable.

A slice pattern is refutable if any of its fields are refutable.

## 10.12. Path pattern [↵](#10-patterns-)

```
<path-pattern> := <path>
```

A path pattern can match any constant, or struct or enum member that have no fields.

They refer to any of the following:
- [enum](#11120-enum-types-) variants
- [Structs](#unit-structs-)
- [Constants](#79-const-item-)
- Associated constants

If the path contains any path disambigutation, it may only refer to an associated constant.

Path pattern are irrefutable if they point to a struct, an enum variant of an enum with only a single field, or a constant whose type is irrifutable.

## 10.13. Alternative pattern [↵](#10-patterns-)

```
<alt-pattern> := <pattern-no-top-alt> { | <pattern-no-top-alt> }*
```

An alternative pattern is a collection of patterns, where only one needs to match.
When this pattern is used, identifier patterns are only allowed if all subpatterns contain the identifier, and if all of them have the same type.

### 10.13.1. Rules alternative patterns [↵](#1013-alternative-pattern-)

To allow for an alternative pattern `p | q`, `p` and `q` need to follow a set of rules

1. When assuming that type convertions are not allowed, and they are unified in an exact manner, the pattern is ill-formed when:
    - the type inferred for `p` does not unify with the type inferred for `q`
    - the same set of bindings are not introduced in `p` or `q`
    - the type of any 2 bindings with the same names in both `p` and `q` do not unify witheachother in respect of them having:
        - different binding modes
        - different types
2. When checking an expression matching `match e_s { a_1 => e_1, ..., a_n => e_n }`, for each arm `a_i` which contains a pattern in the form `p | q`,
   is considered ill-formed when, at any depth `d` where the pattern of `e_s` at depth `d`, the type of the expression does not unify to `p_i | q_i`.
3. When it comes to deciding the exhaustiveness of a pattern, it can be transformed to a top level-only alternative pattern.
   This can be done for any pattern contain a sub-pattern, e.g. the pattern `a(p | q, ..rest)` can be transformed to `a(p, ..rest) | a(q, ..rest)`.

## 10.14. Type check pattern [↵](#10-patterns-)

```
<type-check-patter> := 'is' <type> [ '@' <pattern> ]
```

A type check pattern can be used to explicitly check for a certain type.
Type check patterns can also be used to check if a DST is a given type.

## 10.15. Guard pattern [↵](#10-patterns-)
```
<guard-pattern> := <pattern> 'if' <expr>
```

Patterns can accept guard patterns to further refine the cirteria for matching a pattern.P
Pattern guards apread after the pattern a consists out of a boolean expression.

When the pattern matches successfully, the pattern guard expression is executed.
If the expression evaluates to `true`, the pattern is successfully matched against.
Otherwise, the next pattern including other alternative patterns with the `|` operator in the same pattern are tested..

A pattern guard may refer to the variable bound within the pattern they follow.
Before evaluating the guard, a shared reference to the scrutinee is taken, and is then used when accessing the variable.
Only when the guard evaluates to `true` is the value moved, or copied without moving out of the scrutinee in case the guard fails to match.
Moreover, by holding a shared reference while evaluating the guard, mutation inside the guard is also prevented.

Any bound values within the guard cannot be used outside of it.

If a `|` is encountered in the expression, it will be interpreted as part of an alternative pattern.
If a `|` is required in the expression, it should be wrapped in a [paranthesized expression](#97-parenthesized-expression--).

Guard patterns are refutable.

## 10.16. Constant patterns [↵](#10-patterns-)

When a constant `C` of type `T` is used, it must adhere to `T is Equality`.

In addition, the constant must have structural equality, which is defined as:
- Any [primitive](#1114-primitive-types-) or [string-slice type](#11111-string-slice-types-), in addition
  - Any floating point value only has structural equality when it is **not** `NaN`, this means `NaN` is not a valid value to be used in pattern, and will generate a compiler error
- Tuples, arrays, and slices have strucutral equality if all their fields/elements have structural equality
- References have structural euqality if the value they point to has structural equality
- Raw pointers have structural equality, if it was defined as a constant integer (and then cast/transmuted)
- Any `struct` or `enum` type which implements `StructuralIdentity` or `StructuralEquality`

# 11. Type System [↵](#tables-of-contents)

## 11.1. Types [↵](#11-type-system-)

```
<type> := <type-no-bound>
        | <trait-object-type>
        | <impl-trait-type>

<type-no-bound> := <type-type>
                 | <parenthesized-type>
                 | <primitive-type>
                 | <unit-type>
                 | <never-type>
                 | <path-type>
                 | <tuple-type>
                 | <array-type>
                 | <slice-type>
                 | <string-slice-type>
                 | <pointer-type>
                 | <reference-type>
                 | <optional-type>
                 | <function-type>
                 | <function-pointer-type>
                 | <closure-type>
                 | <record-type>
                 | <enum-record-type>
                 | <inferred-type>
```

Types are an essential part of any program, each variable, value, and item has a type.
The type defines how a value is interpreted in memory and what operations can be performed using them.

Some types support unique functionality that cannot be replicated using user defined types.

### 11.1.1. `type` type [↵](#111-types-)
```
<type-type> := 'type'
```

Types in themselves are a value of the type `type`.
Where the `type` is itself of type `type`

One caveat with `type` values is that while they can be passed into functions and assigned to variables, they cannot be returned from a function.
This is done to prevent a function from returning divergent types, as they do not play nice with traits.

For example, if we were to have the following:
```
fn gen_type(const id: usize) -> type {
    if id == 3 {
        struct {
            x: f32,
            y: f32,
            z: f32
        }
    } else {
        struct {
            x: f32,
            y: f32,
        }
    }
}
```
Then we would be able to add an implementation using an `impl` block, where `id` is a generic parameter, like:
```
impl(id: usize) gen_type(id) {
    fn(&self) get_z() -> f32 {
        self.z
    }
}
```
An issue occurs when we want to use the `z` field, since we can not infer whether the `z` field exists when a type generated by `gen_type` is used, instead we can only check it whenever get_z() is called.
We cannot guarantee that the implementation is valid beforehand, if this were to be nested deep withing a call stack, the errors can be extremely unclear.

> _Note_: In this case, we can do something similar by having a type with a `z` property that is constraint by a generic argument, i.e.
> ```
> struct ty[id] { arr: [if id == 3 { 3 } else { 2 }]f32 }
> impl(id: usize) ty(usize) {
>     property z = arr[2] where id == 3;
> }
> ```
> With this, the above impl can error on definition, as we are missing the constraint required to access `z`
>
> The above code may still result in an error if `arr` would be indexed out of bounds, but it allows a contraint to be added to the property to prevent this

An ever more difficult example would be the following:
```
fn get_type(const id: usize) -> type {
    if id == 0 {
        i32
    } else {
        struct {
            a, b: i32
        }
    }
}
```
In this case, if we want to implement anything on it, there is no clear way of how to handle this, as a primitive type and a structure act very differently.
A primitive type is directly a direct value, while the struct has fields that need to be able to be accessed.

> _Note_: Although the above example isn't a realistic case, it was chosen as a more extreme case to show what type of issues divergent types can lead to, this could instead also be 2 structures that have different field types that act very differently

> _Todo_: if it is realistic to combine divergent types (which is unlikely, as for example in the above example), it might be possible to move type generation from the meta function level to regular code

### 11.1.2. Rescursive types [↵](#111-types-)

Nominal or ordinal types may be recursive, meaning that a type may have member that refers, directly or indirectly, to the current type.
These are some limiations on how types can be nested:
- Type aliases must include a nominal or structural type in the recursion, meaning type aliases, or other types like arrays and tuples are not allowed.
  i.e. `type Foo = &[Foo]` is not allowed.
- The size of a recursive type must be finite, meaning that the recursive field must be 'broken up' by a type like a pointer or reference type.

### 11.1.3. Parenthesized types [↵](#111-types-)

```
<parenthesized-type> := '(' <type> ')'
```

In some locations it may be possible that a type would be ambiguous, this can be solved using a parenthesized type.
For example, a reference to an trait object type with multiple bounds can be unclear, as we cannot cleanly determine if the one of the bounds is a reference, or the whole set of bounds constitute a single type without requiring to rely heavily on context.

### 11.1.4. Primitive types [↵](#111-types-)

```
<primitive-type> := <numeric-type>
                  | <signed-type>
                  | <floating-point-type>
                  | <boolean-type>
                  | <character-type>
```

A primitive type is a type that exists directly within the langauge and can be handled specially by the compiler.
These are commonly types that fit in machine register and have specialized instruction for these types.

Primitive types are susceptible to [undefined behavior]()

> _Todo_: Fix link to undefined behavior section.

> _Todo_: Support for different endianess in types

#### Unsigned types

```
<unsigned-type> := 'u8' | 'u16' | 'u32' | 'u64' | 'u128'
```

An unsigned type represents a natural number (including 0).

Unsigned numbers can generally represent a number between 0 and `(2^n)-1`

Below is a table of supported unsigned integer types:

Type   | Bit width | Min value | Max value 
-------|-----------|-----------|-----------------------------------------------------
`u8`   | 8-bit     | 0         | 255
`u16`  | 16-bit    | 0         | 65_535
`u32`  | 32-bit    | 0         | 4_294_967_295
`u64`  | 64-bit    | 0         | 18_446_744_073_709_511_615
`u128` | 128-bit   | 0         | 340_282_366_920_938_463_463_374_607_431_768_211_455

Both the size and alignment of the unsigned integers are defined by their bit-width.

All but `u128` are generally representable in a CPU register and have native instructions, if any type does not have native instructions, the program will fall back to 'emulating' these types.

In addition to the above types, there is also another unsigned type: `usize`.
`usize` is an unsigned type with the size of a machine-register.
Most commonly, this means that `usize` will be 32-bits on a 32-bit machine, and 64-bits on a 64-bit machine.

> _Note_: Minoa makes no guarantee on the size of `usize` and the programmer will therefore require explicit care when using this type.

> _Note_: A general rule is to prefer unsigned types whenever negative numbers aren't required. The programme does need to pay attention that the result of any intermetiate result cannot be a negative value when unsigned types are used.

#### Signed types

```
<signed-type> := 'i8' | 'i16' | 'i32' | 'i64' | 'i128'
```

An unsigned type represents a integral number.

Unsigned numbers can generally represent a number between `-2^(n-1)` and `(2^(n-1))-1`

Below is a table of supported unsigned integer types:

Type   | Bit width | Min value                                            | Max value 
-------|-----------|------------------------------------------------------|-----------------------------------------------------
`i8`   | 8-bit     | -128                                                 | 127
`i16`  | 16-bit    | -32_768                                              | 32_767
`i32`  | 32-bit    | -2_147_483_648                                       | 2_147_483_647
`i64`  | 64-bit    | -9_223_372_036_854_775_808                           | 9_223_372_036_854_775_807
`i128` | 128-bit   | -170_141_183_460_469_231_731_687_303_715_884_105_728 | 170_141_183_460_469_231_731_687_303_715_884_105_727

Both the size and alignment of the signed integers are defined by their bit-width.

All but `i128` are generally representable in a CPU register and have native instructions, if any type does not have native instructions, the program will fall back to 'emulating' these types.

In addition to the above types, there is also another signed type: `isize`.
`isize` is an unsigned type with the size of a machine-register.
Most commonly, this means that `usize` will be 32-bits on a 32-bit machine, and 64-bits on a 64-bit machine.

> _Note_: Minoa makes no guarantee on the size of `isize` and the programmer will therefore require explicit care when using this type.

#### Floating-point types

```
<floating-point-type> := 'f16' | 'f32' | 'f64' | 'f128'
```

A floating point type represent the same sized type as defined in the IEEE-754-2008 specification.

Below is a table of supported floating-point types:

Type   | Bit width | Mantissa bits      | Exponent bits | Min value  | Max value   | Smallest value | Significant decimal digits | Notes
-------|-----------|--------------------|---------------|------------|-------------|----------------|----------------------------|------
`f16`  | 16-bits   | 10 (11 implicit)   | 5             | 6.55e+5    | -6.55e+5    | 6.10e-5        | 3                          |
`f32`  | 32-bits   | 23 (24 implicit)   | 8             | 3.40e+38   | -3.40e+38   | 1.17e-38       | 6                          |
`f64`  | 64-bits   | 52 (53 implicit)   | 11            | 1.80e+308  | -1.80e+308  | 2.23e-308      | 15                         |
`f80`  | 80-bits   | 1 + 63             | 15            | 1.19e+4932 | -1.19e+4932 | 3.36e-4932     | 15                         | Does not have implicit bit, but explicit integer bit, i.e. `1 + ...`
`f128` | 128-bits  | 112 (113 implicit) | 15            | 1.19e+4932 | -1.19e+4932 | 3.36e-4932     | 34                         |

Both the size and alignment of the floating points are defined by their bit-width.

Most commonly, only `f32` and `f64` are implemented in hardware and have native instructions (like `f80` only being on x86), if any type does not have native instructions, the program will fall back to 'emulating' these types.

> _Note_: Subnormal literals, meaning numbers starting with `0x0.` and followed by any non-zero number in the mantissa and/or exponent, are currently not supported

> _Todo_: could include other floating-point types if wanted

#### Boolean types

```
<boolean-type> := 'bool' | 'b8' | 'b16' | `b32' | 'b64'
```

A boolean type is a primitive type that can be used to define 1 out of 2 possible states: `true` or `false`.
As a boolean only can represent these 2 values, there are also only 2 valid bit representations for a boolean.
These are `0x0` and `0x1`, meaning that the lower bit is set to the value, and all other bits are set to 0.
Any other bitpattern is undefined behavior.

Below is a table of supported boolean types:

Type   | Bit-width | Bit-width in bitfield
-------|-----------|----------------------
`bool` | 8-bits    | 1-bit
`b8`   | 8-bits    | 8-bits
`b16`  | 16-bits   | 16-bits
`b32`  | 32-bits   | 32-bits
`b64`  | 64-bits   | 64-bits

Both the size and alignment of the booleans are defined by their bit-width.
When used in a bitfield, specific bit-with mentioned above is used.

Most commonly, `bool` will be the most common version to use, with sized version mainly being used for 2 reasons:
- `b8` is useful to require booleans to keep a 1 byte width within a bitfield, as `bool` will automatically become a 1-bit value
- in usecase where a specific bit-width is required, e.g. `b32` as an equivalent to Window's `BOOL`

#### Character types

```
<character-type> := 'char' | 'char7' | 'char8' | 'char16' | 'char32'
```

A character type is primitive type that can represent unicode characters.

Below is a table of supported character types

Type     | Meaning           | Bit-width | Bit-width in bitfield | Valid range
---------|-------------------|-----------|-----------------------|------------------------------------------
`char`   | unicode codepoint | 32-bits   | 32-bits               | 0x000000 - 0x00D7FF & 0x00E00 - 0x10FFFF
`char7`  | 7-bit ANSI        | 8-bits    | 7-bit                 | 0x00     - 0x7F
`char8`  | 8-bit ANSI        | 8-bits    | 8-bits                | 0x00     - 0xFF
`char16` | utf-16 codepoint  | 16-bits   | 16-bits               | 0x0000   - 0xFFFF
`char32` | uft-32 codepoint  | 32-bits   | 32-bits               | 0x000000 - 0x10FFFF

Both the size and alignment of the characters are defined by their bit-width.
When used in a bitfield, specific bit-with mentioned above is used.

If a character has a value outside of its valid range, it is [undefined behavior]().

> _Todo_: Fix undefined behavior link

### 11.1.5. Unit type [↵](#111-types-)

```
<unit-type> := '(' ')'
```

The unit type is a special type representing a zero-sided type.
This is also known as `void` in some other languages.

### 11.1.6. Never type [↵](#111-types-)

The never type is a special type that represents an operation that can never complete.
This type can be implicitly coerced into any type.
It can only ever appear as the return value of a function and can therefore not be part of any type, meaning you can only ever return a never type.

```
<never-type> := '!'
```

### 11.1.7. Path types [↵](#111-types-)

```
<path-type> := <type-path>
```

A path type isn't a type by itself, but it refers to another type, either directly to:
- [opaque types](#11115-opaque-types-)
- [struct types](#11115-struct-types-)
- [union types](#11116-union-types-)
- [enum types](#11117-enum-types-)
- [bitfield types](#11118-bitfield-types-)

Or indirectly though:
- [type aliases](#74-type-aliases-)
- [distinct types](#741-distinct-types-)

### 11.1.8. Tuple types [↵](#111-types-)

```
<tuple-type> := '(' <type> { ',' <type> }+ [ ',' ] ')'
              | '(' <type> ',' ')'
              | <named-tuple>
```

A tuple type is a _structural_ type consisting out of a list of types.
They are generally consist out of a minimum of 2 sub-types, as otherwhise they can be resolved to the following types:
- 0: Unit type
- 1: Parenthesized type

A single field tuple is possible, by having the tuple consist of a single type with a trailing comma.

The resulting tuple has a number of fields, that is specified by the number of types contained within the tuple.
The number of field also defined the _arity_ of the tuple, meaning that a tuple with `n` types, is an `n`-ary tuple.
For example, a tuple with 3 types is a 3-ary tuple.

A tuple's fields can be access by their index, meaning that the first field will be `0`, the second will be `1`, etc.
Each field has the type as the one in the same position as is provided in the list of types.

Some examples of tuples:
```
(i32, ) // A 1-ary tuple of type i32
(f64, f64)
(bool, i32)
(i32, bool) // Different then the one above it
(f32, i32, ?String)
```
And some tuple-like types, which are not actually tuples:
```
() // not a tuple, but a unit type
(i32) // not a tuple either, but a parenthesized `i32`
```

An example of a usecase for a 1-ary tuple would be when planning to possibly add another type to a tuple in the future, without breaking code.
This is explained in the following pseudo-code:
```
// When starting with the following code

fn foo() -> (i32, ) { .. }
fn bar() {
    let tup = foo();
    do_something(tup.0);
}

// It is now possible to add an additional return to foo, without breaking already existing code.
fn foo() -> (i32, i64) { ... }

// bar does not need to be changed, as the access to tup.0 is still valid.
```

> _Note_: Internally, tuple types are represented as [record tuple structs](#record-tuple-struct), for example:
> ```
> (i32, i64, bool)
> // is represented at
> record struct __generated_name(i32, i64, bool)
> ```

> _Note_: For a nominal version of a tuple, see [tuple structs](#752-tuple-structure-)

#### Named tuples [↵](#1118-tuple-types-)

```
<named-tuple-type> := '(' <name> ':' <type> { ',' <name> ':' <type> }+ [','] ')'
                    | '(' <name> ':' <type> ',' ')'
```

A named tuple is a variant of a tuple, where each field can have an associated name.
In addition to accessing a field by its name, a named tuple's fields can also be indexed like a regular tuple, i.e. by each element's index.

A named and regular tuple may be assigned to each other when their types match, but 2 names tuples can only be assigned to each other when both their names and types match.

Internally, tuple types are represented as [record tuple structs](#record-tuple-struct), for example:
```
(i32, i64, bool)
// is represented at
record struct __generated_name(i32, i64, bool)
```

> _Note_: Internally, tuple types are represented as [record tuple structs](#record-tuple-struct), for example:
> ```
> (a: i32, field: i64, name: bool)
> // is represented at
> record struct __generated_name(a: i32, field: i64, name: bool)
> ```

### 11.1.9. Array types [↵](#111-types-)

```
<array-type> := [ 'sparse' ] '[' <expr> [ ';' <expr> ] ']' <type>
              | <key-value-array>
```

An array type (`[N]T`) is a fixed-size sequence of `N` elements of type `T`
Array types are laid out as a contiguous chunk of memory.

An array's size expression needs to be a value that can be evaluated at compile time, and has a type of `usize`.

Arrays support out-of-bound safety checks.

> _Todo_: Enumerated arrays, i.e. [Enum]T

#### Sentinel-terminated arrays [↵](#1119-array-types-)

An array can also have a sentinel value, which is declared after the size.
So an array `[N;M]T` has `N` elements of type `T`, with a sentinel value of `M`.
Like the size, the sentinel value needs to be evaluated at compile time, and has a type of `T`.

When a sentinel value is defined, the array will contain 1 additional element past its lenght, this is the sentinel value.

Sentinel value mainly exist for interoperability with C and OS libraries that commonly expect a range of values ending in a sentinal value,
but these are not that useful when writing Minoa code itself

#### Enumerated arrays [↵](#1119-array-types-)

In addition to `usize` sizes, a fieldless enum may also be used as the size of an array, this is done by supplying the type of the enum as the size expression.
This allows the array to be directly indexed using an enum's discriminant value.

By default, the array will have the size of the largest discriminant value within it, + 1.

If the enum were to contain non-contiguous values, an enumerated enum may be prefixed using the `sparse` keyword.
This means that each enum value will be mapped to a corresponding index.
In this case, the array will have the size of the number of variants within the enum.

> _Note_: Sparse arrays do come with a slight overhead for the translation of an the enum's value to the actual index.

#### Key-value arrays [↵](#1119-array-types-)
```
<key-value-array> := '[' ':' <type> ']' <type>
```

A key-value array is a special variant of an array which stores key-value pairs.


### 11.1.10. Slice types [↵](#111-types-)

```
<slice-type> := `[` [ ';' <expr> ] `]` <type>
```

A slice type (`[]T`) is a dynamically sized type that represents a 'view' within a sequence of elements of type `T`.

A slice can be empty, meaning that is has a size of 0 and does not point to any values.

Slices are generally used through reference or pointer-like types:
- `&[]T`: a shared slice, often just called a slice. It borrows the data it point to.
- `&mut []T`: a mutable slice. It mutably borrows the data it point to.

#### Sentinel-terminated slices [↵](#11110-slice-types-)

Like an array, a slice may also include a sentinel value, the slice will then contain 1 additional elements past its dynamically stored length, this is the sentinel value.
This value is meant to prevent accidental out of bounds writes.

Sentinel value mainly exist for interoperability with C and OS libraries that commonly expect a range of values ending in a sentinal value,
but these are not that useful when writing Minoa code itself

See the [index expression](#912-index-expression-) for more info about how to create a sentinal terminated array.

### 11.1.11. String slice types [↵](#111-types-)

```
<string-slice-type> := 'str' | 'str7' | 'str8' | 'str16' | 'str32' | 'cstr'
```

A string slice typre repesents a special slice, encoding a string.
This is a separate type, which allows string specific functionality.
In addition, they assure that the underlying data is valid data for their representive encoding type.

Calling a string slice method on invalid underlying data is [undefined behavior]()

String slices, like regular slices, are dynamically sized types and can therefore only be instantiated through a pointer or reference type.

Below is a table of all string slice types

Type    | character type | internal representation | Meaning
--------|----------------|-------------------------|-----------------------------------------
`str`   | `char`         | `[]u8`                  | utf-8 string
`str7`  | `char7`        | `[]char7`               | utf-7 string
`str8`  | `char8`        | `[]char8`               | ANSI string
`str16` | `char16`       | `[]char16`              | utf-16 string
`str32` | `char32`       | `[]char32`              | utf-32 string
`cstr`  | `char8`        | `[;0]char8`             | C-style string, includes null-terminator

> _Todo_: Fix undefined behavior link

### 11.1.12. Pointer types [↵](#111-types-)

```
<pointer-type> := '^' [ <ptr-specifiers> ] <type>
                | '[' '^' [ ';' <expr> ] ']' [ <ptr-specifiers> ] <type>
<ptr-specifiers> := [ <ptr-align> ] [ 'allowzero' ] [ 'mut' | 'volatile' ]
<ptr-align> := 'align' '(' <expr> ')'
```

A pointer type represents an address in memory containing hte underlying type.
Copying or dropping pointer has no effect on the lifecycle of any value.
Derefencing a pointer is an `unsafe` operation.

Although raw pointers are not neccesarily discouraged, they do lack the safety guarantees that are associated with references.
Meaning anything can happen with the underlying memory, like being modified by another part of the processor.
It is therefore encouraged to use references over pointers whenever possible.

Raw pointer are generally discourages, and are mainly there to allow for interopterability and perfomance-critical and low-level functionality.
It is preferable to use smart pointer wrapping the inner value.

When comparing pointers, they are compared by their address, rather than what they point to.
When comparing pointers to dynamically sized types, they also have their additional metadata compared.

A pointer cannot contain a 'null' value when not in an optional type.

Minoa has 3 kinds of pointers.

> _Todo_: Should pointers handle ownership, some language item, or should it be something API based?
 
#### Single element pointers [↵](#11112-pointer-types-)

A single element pointer `^T` or `^mut T`, refers to exactly 1 element in the memory pointed to.

This pointer can be converted to a reference by re-borrowing it using `&^` or `&mut ^`.

Single element pointers do not allow any arithmetic between pointer and integers, and therefore only support subtracting pointer from eachother, i.e. `ptr - ptr`.
Single element pointers are allowed to be sliced to a single element slice.

As an example, the pointer `^i32` would represent a pointer to a single immutable `i32` value.

#### Multi element pointers [↵](#11112-pointer-types-)

A multi-element pointer `[^]T` or `[^]mut T`, pointing to an unknown number of elements.

In addition to the operations allowed on a single element pointer, a multi-element pointer allows additional functionality:
- pointer and integer arithmetic
- slicing with any range
- indexing of the underlying memory

When a pointer contains dynamically sized types, it will consist out of an array of fat pointers.

Multi-element pointers require a type with an known size and alignment, meaning that multi-element pointer of dynamically sized types are not allowed.

Unlike slices, mutli-element pointers do not support safety checks.

As an example, the pointer `[^]i32` would represent a pointer to an unknown number of immutable `i32` values.

#### Sentinel-terminated pointers [↵](#11112-pointer-types-)

A sentinel terminated pointer `[^;N]T` or `[^;N]mut T` is very similar to a multi-element pointer.
The main difference lies in the fact that a sentinel terminated pointer will only contain the number of elements until the first occurance of the sentinel value.

The main purpose of this type is to prevent buffer overflows when interacting with C-style and OS code.

#### Volatile pointers [↵](#11112-pointer-types-)

Pointers operations are generally assumed not to have any side-effects, and the optimizer uses that fact to be able to optimize the code.
An issue occurse when the pointer points to memory that does have side effects, like MMIO (Memory Mapped I/O).

To handle this correctly, `volatile` pointers can be used. They are pointer that are allowed to have side-effects.
Both single and multi-element pointers support volatile.
`volatile` implies `mut`.

> _Note_: `volatile` pointers are unrelated to atomics, in usecases where `volatile` is used with atomics, this is most likely in error.

#### Alignment [↵](#11112-pointer-types-)

Each type has an alignment as defined [here](#1141-size-and-alignment-), which also means that pointer pointing to memory that contains those values, needs to aligned correctly.

Because of this, each pointer type has an alignment associated with it, by default this is the alignment of the underlying type.
Alignment can be added decided manually by using the `align` pointer specifier.


Since alignment cannot be guaranteed to be at an address with greater alignment, pointer can only be implicitly cast to another pointer with the same alignment of less.
If the programmer can guarantee a pointer has a larger alignment, the core library contains use to cast a pointer with a lower alignment to one with a larger alignment.
If the value given does not adhere to the larger alignment, either an error value can be created, or [illegal behavior might occur](#2332-incorrect-pointer-alignment)

> _Todo_: Specify which utilites

#### `allowzero`

`allowzero` is a special specifier that allows zero to be a valid pointer value.
This should not be confused with optional pointers, but is meant for cases, like an RTOS where the address is mappable.

Optional pointer with `allowzero` have a different size then those without `allowzero`.

#### Provenence [↵](#11112-pointer-types-)

Pointer provedence can be seen as some additional invisible data that is associated with a pointer, this can, for example, be information where a pointer comes from.
Given 2 pointer, there is no guarantee they are meant to be similar, even if they have the same value.

An example of this would be:
```
a := [1, 2, 3];
b := [4, 5, 6];

let p : [^]i32 = &a;
let q : [^]i32 = &b;

r := q + 3;

#assert(p == q);
```
Even though both `p` and `q` originally pointed to an element in 2 distinct sets of memory, the addition of 3 elements to `q` makes it overlap with `p` (as the stack grows downwards).
This can cause issues, as the compiler can see that `p` and `q` are supposed to point to 2 distinct array, so it can interpret this by assuming that the address of `p` and `q` can never overlap.

In an actual usecase, we can derive that they will overlap, as we have a compile time known offset, but if the `3` is a the result of a more complex runtime computation, we suddenly don't have this guarantee anymore (this is ignoring the fact we could actually insert a safety_check on the value we get out of the computation here).

> _Note_: This section is currently mainly informational, as provenence handling is still unresolved. An intersting writeup around this problem can be found [here](https://www.ralfj.de/blog/2022/04/11/provenance-exposed.html). Provence will be something that will be looked at later in the development cycle.

### 11.1.13. Reference types [↵](#111-types-)

```
<reference-type> := `&` [ 'mut' ] <type>
```

A reference type, like a pointer, points to a given value in memory, but of which the memory is owned by another value.
Copying a reference is a shallow opertion and will only copy of just the pointer to the memory, and any metadata required for dynamically sized types.
Releasing a reference has no effect on the lifecycle of the value it points to, except when referencing a temporary value, then it will keep the temporary value alive during the scope of the reference itself.

References are split into 2 types:

#### Shared reference [↵](#11113-reference-types-)

A shared reference prevents direct mutation of the value, but interior mutability provides an exception for this in certain circumstances.
As the name suggets, any mubmer of shared references to a value may exist.

A shared reference is written as `&T`.

#### Mutable reference [↵](#11113-reference-types-)

Mutable references (which haven't been borrowed) allow the underlying value to be directly modified.

A mutable reference is written as `&mut T`.

#### Shared xor mutable [↵](#11113-reference-types-)

One of the rules references introduce for safety reasons, is that any value can only be shared or mutable at any time, and not at the same time either.
This add a guarantee that any shared reference will contain the same underlying value whenever it is accessed, no matter what other parts of the processors are using it for..

#### 11.1.14. Optional types [↵](#111-types-)

```
<optional-type> := '?' <type>
```

An optional type allows a value to be represented using a `null` or `.None` state, which can be used to represent a type with no value set.

Optional types allows the ability to have pointers that cannot be `null` ([`allowzero` pointers](#allowzero) have a different meaning).

When an optional type (or the `Option(T)` type) is used, then depending on the value within, the compiler is allowed to do certain optimizations to encode the 'null' state within the value.
An example is a optional pointer, where the 'null' state is represented with an address of `0x00000000`.

This is synctactic suger of `Option(T)`, which comes with some additinal features, as the compiler understands nullable types. These are the following:
- `None` can be used to set the value to none, and `Some(...)` to set the value to a valid value, as they are imported via the core prelude
- when assigning a non-`.None` value, impliclty warps the value within `.Some(...)`. This also works for returns
- control flow expression have additional syntax to make using them more ergonomic

### 11.1.15. Result types [↵](#111-types-)
```
<result-type> := <type> '!' [ <type> ]
               | '!' <type>
```

A result type allows a value to represent either a valid value or an error.

The syntax is similar to a never type, but is distinguished by having an explicit type on at least one side of it.
The value on the left represents the `T` or valid value, and the value on the right, the `E` or error value.
Meaning that a `Result(T, E)` is represented as `T!E`

When a result type (or the `Result(T, E)`) is used, the type of the value and error, the compiler is allowed to do certain optimization to encode both values in a more compact way.
For example when `E` equals to `()`, and the other value is a type like a pointer, where the 'err' state is represented with the address of `0x00000000`.

This is syntactic sugar for `Result(T, E)`, which comes with some additinal features, as the compiler understands result types. These are the following:
- `Ok(...)` can be used to set the result to a valid value, and `Err(...)` to set the result to an error, as they ar eimported via the core prelude
- when assigning a value, it can implicitly wrap a valid value with `.Some(...)`. This also works for returns
- control flow expressions have additional syntax to make using them more ergonomic

### 11.1.16 Opaque types [↵](#111-types-)
```
<opaque-type> := 'opaque' [ '(' <expr> ')' ]
```

An opaque type represents a type with an unknown layout, which can either be dynamically or statically sized.
If no size is given, the opaque time has an unknown, but non-zero size.
If a size is set, the size must be given by a compile time evaluated expression.

> _Note_: Internally, opaque types are represented:
> - when sized as `[N]u8`, where `N` is the size of the opaque type
> - when unsized as `dyn ?Sized`

A sized opaque type should be prefered when the size it would take up is known and its use location requires to be sized.
Otherwise an unsized opaque type should be used.

### 11.1.17 Struct types [↵](#111-types-)
```
<struct-type>   := [ 'mut' ] 'struct' [ <generic-params> ] '{' [ <struct-members> ] '}'
<struct-members>:= [ <struct-fields> ] { <assoc-item> }*

<struct-fields> := <struct-field> { ',' <struct-field> }* [ ',' ]
<struct-field>  := { <attribute> }* [ <vis> ] [ 'mut' ] <field-names> ':' <type> [ <struct-field-tag> ]
                | { <attribute> }* [ <vis> ] [ 'mut' ] <name> ':' <type> [ '=' <expr> ] [ <struct-field-tag> ]
<field-names>   := <name> [ ',' <name> ]
```

A structure type represents a collection of named fields, and associated items.
The layout of a structure is undefined by default, allowing the compiler to do optimization like field reordering. The layout can be specified using the [`repr` attribute](#repr).

The struct needs to define all fields before any item in the struct, if any field occurs after an item will result in an error.

Each fields at minimum defines a name (or multiple), and the type of the field.
Each field can then define its visibility, which controls in which locations the field may be accessed.
In addition, a field may be made mutable, allow it to be modified after the structure is initially created, as by default each field may only be assigned when defining it in a [struct expression](#9113-struct-expressions-), and may not be modified later on.

> _Note_: struct fields may not have an anonymous generic struct type.

In addition, the entire struct type may also be declared as mutable, this will propagate the `mut` property to all fields, as follows:
```
mut struct {
    pub a: i32,
    b: i32,
}
// Will be converted to
struct {
    pub mut a: i32,
    mut b: i32,
}
```

Each field definition may contain multiple names, this will result in a field to be created for each, with the type that was defined.
When multiple names are used, both the visibility and mutability will also be propagated to the created fields
Below is an example of this.
```
struct {
    pub mut a, b: i32,
}
```
Will be equivalent to
```
struct {
    pub mut a: i32,
    pub mut b: i32,
}
```

A field may also be a placeholder field, by using `_` as its name, this means that the field will take up space within the structure, but will not be accessible.
This can be used for example as padding in a [`packed`](#packed) struct, or as a reserved value for future use.

After all fields are declared, a struct may also define associated items.

> _Note_: A [struct item](#75-structs-) allows for an easy way of defining structures.

> _Todo_: A link to associated items

#### Default struct fields [↵](#11116-struct-types-)

When a field has only a single name defined for it, the field may be provided with a default value, this means that when a field is not explicitly initialized, i.e. when left out, the field will gain this value.
The default value needs to be known at compile time, in case a field should have a default value that can only be calculated at runtime, a function can be used.

> _Note_: Default values for fields should not be confused with the value of fields if the `Default` trait is implemented.
> Field default values are used to allow them to be omitted when constructing a new struct, not to retrieve a default value for the entire struct,
> this means that `Default::default()` may return a different value that a field's individual default value, as it is allowed to decide these values at runtime.

```
// Foo has a default value for `z`, as this one can be calculated at compile-time
struct Foo {
    x: i32,
    y: i32,
    z: i32 = 3,
}

// If now we want `y` to always have a default value, but it requires to be calculated at runtime, we would use a function instead
impl Foo {
    pub fn new(x: i32) -> Self {
        Self {
            x,
            y: runtime_calculation(),
            // z: 3 <- default value is always 3
        }
    }
}
```

#### Use fields [↵](#11116-struct-types-)
```
<struct-use-field> := { <attribute> }* [ <vis> ] [ 'mut' ] [ 'extend' ] 'use' [ <name> ':' ] <path>
```

While a structure cannot derive from another structure, it is able to include the content of another struct in its body and even take over functionality.
This can be done using a `use` field, in a structure, `use` does not import symbols, but creates a use field.

A use field can be named, in addition to be able to access the include struct's method directly, the contents of the struct has to be access via its name, as if it were a member.
Either will still refer to the same data, meaning:
```
struct Bar {
    a: i32
}

struct Foo {
    x: f32,
    // Named use field
    use bar: Bar,
}

foo := Foo { x: 2.0, a: 3 };
assert(addr_of(foo.x) == addr_of(foo.bar.x));
```
> _Todo_: Fix `assert` and `addr_of` syntax

If a use field contains a variable with the same name of another variable, or another variable in another use field, an error will be emitted.
This can be resolved by having a named use field, but a warning will still be generated.
```
struct Foo {
    a: i32,
    b: i32
}

struct Bar {
    b: i32
}

struct A {
    a: i32,
    use Foo, // error: field `Foo.a` overlaps field `a`
}

struct B {
    use Foo,
    use Bar, // error: field `Bar.b` overlaps field `Foo.a`
}

// This also happens for named use fields
struct A {
    a: i32,
    use foo: Foo, // warning: field `foo.a` overlaps field `a`, and can therefore only by accesed via `A.foo.a`
}
```

The above struct can  also be initialized via the include struct's name, as shown below:
```
bar := Bar { a: 4 };
foo := Foo { x: 1.0, bar };
```

The following restrictions are applied to the `use` field's included struct:
- All fields need to be visible to the structure with the field
- Only items visible to the structure with the field will be implemented.

The visibility and mutablity of the fields depends on the visibility and mutability provided to the included struct.
These are applied to all fields within the included struct, and to the struct name (if provided).

Visibility and mutability will be applied in the following way:
- A field's visibility will be the [common denominator](#162-common-denominator-) of the field visibility and the provided visibility
- A field's mutability will be a combination of the provided mutability and the field's visibility within the included struct, meaning both must be `mut` for the included field to be `mut`

In addition, the functionality of the included struct can be automatically implemented on the new struct, by using `extend`.
The visibility of these items will be applied in the same way as that of field, using the [common denominator](#162-common-denominator-) of the visibilities.

Only items that would not override an already existing item will be implemented.
If multiple `extend` use fields exists, and an item would conflict, an error will be produced.
```
struct Foo {
    x: i32

    impl {
        fn foo() {}
    }
}

struct Bar {
    y: i32

    impl {
        fn foo() {}
        fn bar() 
    }
}

struct A {
    extend use Foo,
    extend use Bar, // error: Conflicing item `fn foo()`
}

struct B {
    use Foo,
    extend use Bar, // While `Bar` contains `foo`, it's explicitly overwritten by `B`, so `Bar.foo` will be ignored

    impl {
        fn foo() {}
    }
}
```

#### Fields tags [↵](#11116-struct-types-)
```
<struct-field-tag> := <string-literal> | <raw-string-literal>
```

Struct field tags allow arbitrary meta-information to be added to individual fields, which can be accessed using reflection.

#### Record structs [↵](#11116-struct-types-)
```
<record-struct-type>   := 'record' 'struct' '{' <record-struct-fields> { <assoc-item> }* '}'
<record-struct-fields> := <record-struct-field> { ',' <record-struct-field> } [ ',' ]
<record-struct-field>  := <field-names> ':' <type> [ <struct-field-tag> ]
```
A record structure, also known as a POD (Plain Old Data) struct, is a variant of a struct which is _structural_ instead of _nominal_.
These structs follow the rules defined [here](#113-nominal-vs-structural-types-).

Some of the most notable ones for structs are:
- all fields are public
- all fields are mutable

#### Unit structs [↵](#11116-struct-types-)
```
<unit-struct-type> := 'struct' '{' [ <struct-element> ] '}'
```

A unit struct is a variant of a struct containing no fields, and which can be initialized using just their name.
They can be seen as distinct type aliases to a unit type, but with the ergonomics afforded to any structure type.

A unit struct cannot be anonymous and must be associated with a type alias.

#### Generics in struct types [↵](#11116-struct-types-)

Struct types may also contain generic arguments, how this is handled depends on whether it is assigned to a type alias.
If it is assigned to a type alias, the arguments can be directly provided at the usage location of the struct.
When it is directly used as a type of a variable, it must be inferred from the values passed to the value on initialization.

### 11.1.18 Tuple struct types [↵](#111-types-)
```
<tuple-struct>           := [ 'mut' ] 'struct' '(' [ <tuple-fields> ] ')' [ <tuple-stuct-body> ] ';'
<tuple-struct-fields>    := <tuple-field> { ',' <tuple-field> }* { ',' <tuple-struct-def-field> }* [ ',' ]
                          | <tuple-struct-def-field>  { ',' <tuple-struct-def-field> }* [ ',' ]
<tuple-field>            := [ <vis> ] [ 'mut' ] [ <name> ':' ] <type>
<tuple-struct-def-field> := [ <vis> ] [ 'mut' ] [ <name> ":" ] <type> '=' <expr>

<tuple-struct-body>      := '{' { <struct-member> } '}'
```

A tuple struct type is similar to a struct type, with some differences.
Like both structs, the internal representation of a tuple struct is by default undefined, allowed for the same compiler optimizations.

Each field within the tuple struct can have its visibility and mutability defined.
Each field can be accessed using a [tuple index expression](#913-tuple-index-expression-), in addition, they may also have an optional name, which may be used to access a field via a [field access expression](#916-field-access-).

Similar to a regular stuct type, the entire tuple struct type may be marked as `mut`, which will then propage the `mut` property to all fields.

Tuple struct allows associated item to be added at the definition's location, by adding a special body after the tuple declaration.

#### Default tuple struct fields [↵](#11117-tuple-struct-types-)

A field may contain a default value, if they are placed at the end of the tuple fields, and may only followed by other fields with a default value.
Meaning that when these fields are left out during initialization, they will gain this value.
The default vbalue needs to be known at compiler time, in case a field should have a default value that can only be calulated at runtime, a function can be used.

> _Note_: Default values for fields should not be confused with the value of fields if the `Default` trait is implemented.
> Field default values are used to allow them to be omitted when constructing a new struct, not to retrieve a default value for the entire struct,
> this means that `Default::default()` may return a different value that a field's individual default value, as it is allowed to decide these values at runtime.

```
// Foo has a default value for the last field, as this can be calculated at compile time
struct Foo(i32, i32, i32 = 3);

// If we now want the 2nd field to always have a default value, but is required to be calculated at runwime, we would use a function instead
impl Foo {
    pub fn new(x: i32) -> Self {
        Self (
            x,
            runtime_calculation,
            // 3 <- default value is always 3
        )
    }
}
```

#### Record tuple structs [↵](#11117-tuple-struct-types-)
```
<record-tuple-struct>           := 'record' 'struct' '(' [ <tuple-fields> ] ')' ';'
<record-tuple-struct-fields>    := <tuple-field> { ',' <record-tuple-field> }* { ',' <record-tuple-struct-def-field> }* [ ',' ]
                                 | <record-tuple-struct-def-field>  { ',' <record-tuple-struct-def-field> }* [ ',' ]
<record-tuple-field>            := [ <name> ':' ] <type>
<record-tuple-struct-def-field> := [ <name> ":" ] <type> '=' <expr>
```

A record tuple struct, also known as a POD (Plain Old Data) tuple struct, is a variant of a tuple struct which is _structureal_ instead of _nominal_.
These tuple struct follow the rules defined [here](#113-nominal-vs-structural-types-).

Some of the most notable ones for tuple structs are:
- All fields are public
- All fields are mutable

### 11.1.19 Union types [↵](#111-types-)
```
<union-type>    := [ 'mut' ] <union> '{' <union-elements> '}'
<union-element> := <union-field> { ',' <union-field> }* [ ',' ] { <assoc-item> }*
<union-field>   := { <attribute> }* [ <vis> ] [ 'mut' ] <name> ':' 
```

A union is a struct-like type, but instead of all fiels being sequential, a union's main characteristic is that all fields share a comon storage.
As a result, a write to any field can overwrite all other fields.
Union must always contain at least 1 field to be valid.

> _Note_: union fields may not have an anonymous generic struct type.

Each field may be made mutable, allow it to be modified after the structure is initially created, as by default each field may only be assigned when defining it in a [struct expression](#9113-struct-expressions-), and may not be modified later on.
In addition, the entire union type may also be declared as mutable, this will propagate the `mut` property to all fields, as follows:
```
mut union {
    pub a: i32,
    b: f32,
}
// Will be converted to
union {
    pub mut a: i32,
    mut b: f32,
}
```

Union field are restricted to the following sub-set of types:
- `Copy` types (including records)
- Referecnes (`&T` and `&mut T` for an arbitrary type `T`)
- Manually droppable values
- Tuples and array containing values that adhere to these restrictions

> _Todo_: Specify util for 'Manually droppable values'

These restriction ensure that no value in a field needs to be dropped.
It is also possible to manually implement `Drop` on union, as by default, union fields will never be dropped.

#### Union field access [↵](#11118-union-types-)

Because of the common storage of unions, when initializing a union, only 1 field may be initialized.
The field that is initialized is said to be the `active` field of the union.

> _Note_: Unions do not have an actual notion of an `active` field, i.e. no metadata is carried to specify the active field, so there is no special meaning.
>         The term is meant to specify to a programmer what union field is currently assigned and has a valid value.

While any member can be accessed at any time, by reading a value from the underlying memory, it is recommended only to ever access the 'active' value.
This is because any field with an incompatible layout with the active field **may** contain invalid data.

Because of this, reading a union field is an `unsafe` operation.
Writing to a union field is always safe on the other hand, as it overwrites arbitrary data.

Reading a field that is not the active field in the union is illegal behavior.

Safety checks will always be added when the compiler is able to figure out whether a field is accessed incorrectly from the surrounding context.
While it is not possible in compiled code, when the code is run through an interpreter, it is allowed to store additional metadata separatly from the data, and add safety check at an access site.

Unless a union has a `C` representation, it is also illegal behavior to transute the value to any type, as there is not guarantee that all types are stored at a zero-offset.
With a `C` representation, reading from the union is analogous to transmuting its data to one of the fields types.

#### Pattern matching on unions [↵](#11118-union-types-)

Unions can also be accesed inside when pattern matching, with the restiction that exactly one field is allowed to be matched at any time.
Since reading from a pattern field is unsafe, the corresponding match must be done in an unsafe context.

Pattern matching directly on a union is not allowed, it is only allowed when it is part of a larger structure when there is a separate disambiguation in addition to the union, like a value coming from an FFI context.

For Example
```
union U {
    i: i32,
    f: f32
}

u := U{ i: 1 };

// error: cannot match on a union
unsafe {
    match u {
        .{ i: 1 } => (),
        .{ f } => (),
    }
}

enum Tag { I, F };
struct Value {
    tag: Tag,
    u:   U,
}

val := Value { tag: Tag.I, u };
// Fine, as we aren't directly switching on a union, but a stucture containin a union
unsafe {
    match val {
        .{ tag: .I, u: .{ i: 1 } } => (),
        .{ tag: .F, u: .{ f } } => (),
        _ => ()
    }
}


// But the following will:
// error: cannot match on a union field only
unsafe {
    match val {
        .{ u: .{ i: 1 }, .. } => (),
        _ => ()
    }
}
```

#### Reference to union fields [↵](#11118-union-types-)

Since union fileds share a common storage, gaining writing access to one field of hte union can give write access to all its remaining fields.
Therefore, if any field is borrowed mutably, no ther field can be borrwoed mutably at the same time.

### 11.1.20 Enum types [↵](#111-types-)
```
<enum-type>           := [ 'mut' ] 'enum' '{' <enum-members> '}'
<enum-members>        := <enum-variant> { ',' <enum-variant> } [ ',' ] { <assoc-item> }*

<enum-variant>        := <unit-enum-variant>
                       | <tuple-enum-variant>
                       | <struct-enum-variant>

<unit-enum-variant>   := <name> [ '=' <expr> ] [ <field-tag> ]
<tuple-enum-variant>  := [ 'mut' ] <name> '(' <tuple-field> { ',' <tuple-field> }* [ ',' ] ')' [ '=' <expr> ] [ <field-tag> ]
<struct-enum-variant> := [ 'mut' ] <name> '{' <struct-field> { ',' <struct-field> } '}' [ '=' <expr> ]
```

An enum type, also known as an enumeration, is a collection of distinct values, called variants, which may have additional data attached to them, known as a variant's payload.
Since an enum can carry a payload, this is also sometimes know as an algebraic data type.

Each variant is also its own 'constructor', creating an enum value of the given variant with its associated data.
Enums are particularly useful in when used for pattern matching.

An enum is required to at minimum have a single variant.

Each variant can be on of the following:
- unit variant, also known as a fieldless variant: declared with just a name, i.e. it has no payload
- tuple variant: a name, followed by a tuple defintion
- struct varaiant: a name, followed by a struct body with only fields

Each field in a variant may be made mutable, allow it to be modified after the structure is initially created, as by default each field may only be assigned when defining it in a [struct expression](#9113-struct-expressions-), and may not be modified later on.

> _Note_: struct fields may not have an anonymous generic struct type.

Defining mutability may also happen on a per-variant basis, or on the entire enum type, this will propagate the `mut` property to all fields of the variant, or the entire enum, respectivly, as follows:
```
enum {
    mut A { b, c: i32 },
    D(i32, f32),
}
// Will be converted to
enum {
    mut A { mut b, c: i32 },
    D(i32, f32),
}

// Or
mut enum {
    A { b, c: i32 },
    D(i32, f32),
}
// Will be converted to
enum {
    mut A { mut b, c: i32 },
    D(mut i32, mut f32),
}
```


Each variant works as its own symbol, and can therefore be imported using a `use`, this allows the variant to be used without preceeding its name by either a path or a inferring `.` (dot).

Each variant may also have a field tag associated with it.

> _Todo_: Could be interesting to add a version with an external discriminant, i.e. a discriminant that's separate from the actual payloads, which isn't just a union.
>         i.e. instead of matching to `{ tag: .Val, union_val: { name: val } }`, we could match on `Val{ name: val }`, i.e. something like `data.with_discriminant(val)`

#### Discriminant [↵](#11119-enum-types-)

Each variant is represented using its discriminant, this is an integer value that logically associates an enum instance with the variant an enum holds.

By default, this value is represented as an `isize` value.
However, the compiler is allowed to substitute this with a smaller integer types that fits all determinants, including unsigned types if no distriminant needs to hold a signed value.
This is in order to reduce the amount of memory that is taken in by each enum instance.

The underlying type discriminant is represented by, can be defined explicitly using a [`repr` attribute](#repr).

> _Todo_ Add support for non-integer discriminants, e.g. char

##### Assigning discriminant values [↵](#discriminant-)

By default, the discriminant is implicitly assigned, each varaint will take on the discriminant of the previous value, plus one.
If the intial variant has no value assigned, it will get a value of 0.

Discriminants can also be assigned explicitly after the variant's name and payload.
The value needs to be a compile-time expression.

There are restrictions to the value of a discriminant, as each variant needs to have a unique value.
Setting 2 variants to the same value will cause the compiler to emit an error.
For example:
```
enum A {
    Variant0 = 1,
    Variant1 = 1, // error: Variant1 has the same discriminant as Variant0
}

enum B {
    Variant0,
    Variant1,
    Variant2 = 1, // error: Variant 2 has the same discriminant as Variant1
}
```

A discriminant value may also not exceed the maximum value that can be stored within its type.
```
@repr(u8)
enum C {
    Val = 256, // error: discriminant value exceeds range
}
```

##### Accessing discriminant values [↵](#discriminant-)

The most common way of getting an enum's discriminant, is using the `discriminant()` utility function.

An enum can also be cast to a discriminants type, but this requires the enum to be a fieldless enum.

The last way is via a pointer cast, but this is only possible with a `@repr(C, ty)` enum, where `ty` is a know integer type.
Then this enum may be cast to a pointer of type `ty`

#### Fieldless enum [↵](#11119-enum-types-)

A fieldless enum is enum where none of its variants have a payload.
Therefore it only exists out of a discriminant, and represent a collection of uniquely named values.

In short, this meant that the enum only has:
- unit-only variants, and/or
- variant with empty payloads

#### Record enum types [↵](#11119-enum-types-)
```
<record-enum-type>           := 'record' 'enum' '{' <enum-members> '}'
<record-enum-members>        := <enum-variant> { ',' <enum-variant> } [ ',' ] { <assoc-item> }*

<record-enum-var  iant>      := <unit-enum-variant>
                             | <tuple-enum-variant>
                             | <struct-enum-variant>

<record-tuple-enum-variant>  := [ 'mut' ] <name> '(' <tuple-field> { ',' <tuple-field> }* [ ',' ] ')' [ '=' <expr> ] [ <field-tag> ]
<record-struct-enum-variant> := [ 'mut' ] <name> '{' <struct-field> { ',' <struct-field> } '}' [ '=' <expr> ] [ <field-tag> ]
```
A record enum is an enum, also known POD (Plain Old Data) enum, is a variant of an enum which is _structural_ instead of _nominal_.

Some of the most notable ones for structs are:
- all fields are public
- all fields are mutable

#### Flag enum types [↵](#11119-enum-types-)
```
<flag-enum>         := 'flag' 'enum' '{' <flag-enum-members> '}'
<flag-enum-members> := <unit-enum-variant> { ',' <unit-enum-variant> }* [ ',' ] { <assoc-item> }* 
```
A flag enum, also known as a bitset, it a variant of a fieldless enum (where only unit fields are allowed), which instead of distinct variants, store a collection of bit-flags.
Each falg enum can contain as many unique flags as are allowed by the underlying primitive type, by default, this value is represented as an `usize` value.
However, the compiler is allowed to substitute this with a smaller integer types that fits all determinants.
The discriminant is always an unsigned type.

When no explicit discriminant is given, a field will always have a value that is the next power of 2, that is greater than the previous variant.
If no explicit discriminant is set of any variant, the first variant will gain a value of 1.
If at any point the next variant's discirimnant would overflow, the compiler will emit an error.

When no variant is explicitly set to a value of 0, the flag enum will automatically as a `.None` flag with a valu of 0.

Unlike regular enums, a flag enum's explicit discriminant may reference other variant in the flag enum.

A flag will always have a set of implicitly generated methods and operators implemented to be able to work with flags.
The following function, operators and traits are implemented:

> _Todo_: Add function, operators, and traits here

### 11.1.21. Bitfield types [↵](#111-types-)
```
<bitfield-type>    := [ 'mut' ] 'bitfield' [ '(' <expr> ')' ] '{' <bitfield-members> '}'
<bitfield-members> := <bitfield-field> { ',' <bitfield-field> } [ ',' ] { <assoc-item> }*
<bitfield-field>   := [ <vis> ] [ 'mut' ] <name> ':' <type> [ '|' <expr> ]
```

A bitfield type is similar to a struct type, but which is tightly packed and allows each field to be offset and sized to a bit (non-byte) value.
By default, a bitfield ignores the alignment of a type.

The bitfield type itself may have it's size explicitly defined, this is the number of bits the type would take up when it would be part of a bitfield.
Outside of anohter bitfield, the bitfield will have a size of the minimum amont of bits needed to store this size.
If the fields take in less bits than are specified, the remaining bits will be padding.
When the size of the fields take up more bits, the compiler will emit an error.

Each field in a bitfield is allowed to explicitly define how it should be packed.
This can be done by providing a compile time expression defining the size of the field.

Like struct fields, each bitfield field is allowed to specify their own visibility and mutability.

Each field within the tuple struct can have its visibility and mutability defined.
Each field can be accessed using a [tuple index expression](#913-tuple-index-expression-), in addition, they may also have an optional name, which may be used to access a field via a [field access expression](#916-field-access-).

For consistency, bits are laid out in the following way:
- Bits go from MSB (most-significant bit) to LSB (least-significant bit)
- Field themselves follow the endianess of the type itself.

#### Record bitfield types [↵](#record-bitfield-types-)
```
<record-bitfield-type> := 'record' 'bitfield' '{' <bitfield-memebers> '}'
```

A record bitfield, also known as a POD (Plain Old Data) bitfield, is a variant of a bitfield which is _structural_ instead of _nominal_.
These bitfields follow the rules defined [here](#113-nominal-vs-structural-types-).

Some of the most notable ones for bitfields are:
- all fields are public
- all fields are mutable

### 11.1.22. Function types [↵](#111-types-)

A function type is an anonymous compiler-generated type, which cannot be manually defined.
The type references a specific function, including its name and its signature (including parameter labels), and it's compile-time parameter values.

Since each function type is specific to each function, a value of this type does not need to use any indirection to be called, as it does not contain an actual function pointer.
This allows this to be a 0-sized type.
Separating each function in its own type additionally allows for better optimization.

When an error message is generated using this type, it will generally show up as something like `fn(param_name:i32) -> i32 { name }`

Since each type is unique to a function, they cannot be mixed or they will result in a type error:
```
fn foo(T: type);
x := &mut foo(i32);
*x = foo(u32); // error: type mismatch
```

Function types are however able to coerce into [function pointer types](#11116-function-pointer-type-) that have a matching signature.
This can happen at the following sites:
- when a function is passed to place where a matching function type is expected
- when a function is returned in different arms of a control flow expression.

During coercion, all constant parameters will be collapsed before coercing, for more info can be found [here]()

> _Todo_: is 'collapsing' correct terminology? + fix link
> _Todo_: We somehow need to bake lifetimes propagation into this type

### 11.1.23. Function pointer type [↵](#111-types-)

```
<fn-type> := [ 'unsafe' [ 'extern' <abi> ] ] 'fn' '(' <fn-type-params> ')' [ '->' <type-no-bounds> ]
<fn-type-params> := <fn-type-param> { ',' <fn-type-param> }* [ ',' ]
<fn-type-param> := { <attribute> }* [ ( <name> | '_' ) { ',' ( <name> | '_' ) }* ':' ] <type>
```

A function pointer type can refer to a function whose identity is not known at compile time.
The can be created via coercion from functions and non-capturing closures with a matching signature.

If a function is marked `unsafe`, it is able to be assgined from both safe and unsafe functions, and must be called from an unsafe context.
To assign a pointer with a specific ABI, the function needs to be marked as an `extern` function with a matching ABI.
If not marked with a ABI, it will default to the `"C"` ABI.

Parameters may contain one or more names, but for the purposes of a function pointer these names are ignored, but are instead useful for documentation.
If multiple names are are given for a single parameter, these will be split into multiple parameters with the same type, matching the amount of names supplied.

With the exception as types within a parameter list, function pointers cannot be stored or passed as their own type, but instead need to be behind either a pointer or reference.
When used in in a parameter list, they represent a closure type, this is then known as a closure param type.

> _Note_: Whenever the term 'raw function pointer type' is used, it represent a free-standing pointer type that is not part of a another type

> _Todo_: Variadic parameters

### 11.1.24. Closure types [↵](#111-types-)

A closure type is an anonymous compiler-generated type, which cannot be manually defined.
Similar to function types, it refers to a closure using a unique anymous type.

When closures don't capture any values, they will be able to coerce into [function pointer types](#11116-function-pointer-type-) that have a matching signature.
Coercion allows these closures' parameter types to be infered based on their use, and they follow the same rule as [function types](#11115-function-types-) as to which sites can cause coercion.

> _Todo_: Capture rules are currently heavily based on rust, this might not be the best solution, as we also want manual control for capturing

> _Todo_: Specify which traits are supported, depending on captures

#### Passing closures to functions

Closures can be passed to a function is 2 ways:
- As a deduces function parameter that implements one of the relavent function traits.
- As a parameter with a so-called [closure param type](#11123-function-pointer-type-)

#### Capture modes [↵](#11123-closure-types-)

Capture modes define how a place expression from the surrounding environment will be borrowed or moved into the closure. The following modes are supported:
1) immutable borrow: the place expression is captured as a [shared referece](#shared-reference-)
2) unique immutable borrow: similar to immutable borrow, but must be unique as defined [here](#unique-immutable-borrows-in-captures)
3) mutable borrow: the place expression is captured as a [mutable reference](#mutable-reference-)
4) move: also known as 'by value'. The place expression is captured by moving it into the closure

The order that is used to decide which method to use, is the same order in order as the modes described above.
This mode is not affected by any of the surrounding code at the location the closure is created.

> _Todo_: We somehow need to bake lifetimes propagation into this type

##### Copy values [↵](#capture-modes-)

Values implementint the `Copy` trait that are moved into the closure, will be captured using immutable borrow.

#### Capture precision [↵](#11123-closure-types-)

A capture path is a sequence starting with a variable from the surrounding environment, followed by zero or more place projectecion taht were applied to the variable.
A place projection is any of the following:
- [field access](#916-field-access-)
- [tuple index](#913-tuple-index-expression-)
- [dereference](#1422-derefence-operator-)
- [array or slice index](#912-index-expression-)

The capture will then use this path to do a partial borrow of the deepest element within the path.
For example:
```
struct Foo {
    bar: (i32, i64),
}
foo := Foo{ f1: (1, 2) };

c := fn{
    x := foo.bar.1; // Captures 'foo.bar.1'
}
```
In the above code, first the local variable `s` is captured, followed by a field access `.bar`, finally a tuple index `.1`.
This results in the closure capturing `foo.bar.1` by an immutable borrow, and out of which it follows that it also partially borrows both `foo` and `foo.bar`.

##### Shared prefix [↵](#capture-precision-)

In a case where a capture path and one of the acvesto's of that paths are both captured by a closure, the ancestor path is captured wit the highest capure mode among the 2 captures, using the ordering:
`immutable borrow < unique immutable borrow < mutable borrow < copy`.

This might be applied recusively:
```
a := "A":s;
b := (a, "B":s);
mut c := (b, "C":s);

let closure = fn{
    foo(&c); // Captures `c` immutably
    modify(&mut b); // Captures `b` mutably
    move_value(a); // Caputes `c` by value
}
```
So this closure will capture c by value.

##### Rightmost shared reference truncation [↵](#capture-precision-)

The capture path is trucated at the rightmost deference in the capture paths if the dereference is applied to a shared reference.

This trunctaon is allowed because fields that are read through a shared reference will always be read via a shared reference or a copy.
This helps reduce the size of the capture when the extra precision does not yeild any benefit from a borrow checking perspective.

The reason it is the rightmost dereference, is is to help avoid a shorter lifetime than is necessary.
Consider the following example:
```
struct Int(i32);
struct B(&i32);

struct MyStruct {
    a: &Int
    b: B
}

fn foo(m: &MyStruct) -> impl FnMut() {
    c := fn{ drop(&m.a.0) };
    c
}
```
If this were to capture `m`, then the closure would not live long enough to be practicle, as its lifetime would depend on the lifetime of `m`.
Where as capturing `m^.a^` as an immutable reference, will allow the closure to live as long as that reference.

> _Todo_: Since this example is based on rust, the syntax may not be right + we aren't necessarily going to have the same lifetime system as rust

##### Wildcard pattern bindings [↵](#capture-precision-)

Closures only capture data that needs to be read. Binding a value with a [wildcard pattern](#103-wildcard-pattern-) does not count as a read, and thus won't be captured.
For example, the follwoing closers will not capture `x`:
```
x := "hello":s;
c := fn {
    _ := x;
}
c();

c := fn { match x {
    _ => do_something(),
}};
c();
```

This also includes destructuring tuples, structs, and enums. 
Fields matched with the [rest pattern](#104-rest-pattern-) are also not considered as read, and thus those fields will not be captured.
The following illustrates some of these:
```
x := ("a":s, "b":s);
c := fn {
    let (first, ..) = x; // Captures x.0 by value
};
// The  first tuple field has been moved into the closure,
// but we are still free to access the second tuple field
do_something(x.1);
c();
```

Partial captures of arrays and slices are not supported. The entire slice or array is always captured even if used with wildcard pattern matching, indecins, or sub-slicing.
For example:
```
struct Example;
x := [Example, Example];

c := fn  {
    let [first, _] = x; // Captures all of `x` by value
};
c();
do_something(x[1]); // error: borrow of moved value: `x`
```

Values that are matched with wildcard mus still be initialized.
```
let x: i32;
c := fn {
    let _ = x; // error: used binding 'x' isn't initialized
}
```

##### Capturing references in move contexts [↵](#capture-precision-)

Because it is nt allowed to move fields out of a refeerence, `move` closures will only capture the prefix of a capture path that runs up to, but not including, the firest derefence of a reference.
The reference iteself will be moved into the closure:
```
struct T(String, String);

mut t := T("foo":s, "bar":s);
t_mut_ref := &mut t;
c := fn { [move] =>
    t_mut_ref.0.push(str: "123"); // captures `t_mut_ref` by value
}
c();
```

##### Pointer dereference [↵](#capture-precision-)

Because it is `unsafe` to dereference a pointer, closures will only capture the prefix of a expression path that runs up to, but not including, the first dereference of a pointer.
```
struct T(String, String);

t := T("Foo":s, "bar":s);
t_ptr := &t as ^T;

c := unsafe fn {
    do_something(t_ptr^.0); // captures `t_ptr` immutably
};
```

##### Union fields [↵](#capture-precision-)

Because it is `unsafe` to access a union field, closures will ohnly capture the prefix of a capture path that runs up to the union itself
```
union U {
    a: (i32, i32),
    b: bool,
}
u := U{ a: (123, 456) };

c := fn {
    x := unsafe { u.a.0 }; // captures `u` by value
}
c();

// This also includes writing to the field
mut u := U{ a: (123, 456) };

c := fn {
    u.b = true; // captures `u` mutably
}
c();
```

##### References to unaligned structures [↵](#capture-precision-)

Because it is [illegal behavior](#2332-incorrect-pointer-alignment-) to create a reference to  unaligned fields in a structure, closures will only capture the prefix of the capture path that runs up to, but not inscluding, the first field access into a `packed` structure or a bitfield.
This inclused all fields, even those that are aligned, to protect against compatibility concerns should any of the fields in the structure of bitfield change in the future.

```
@repr(packed)
struct T(i32, i32);

t := T(1, 2);
c := fn {
    a := t.0; // captures `t` immutably
}
// Copies out of `t` are still ok
a, b := t.0, t.1;
c();
```

Similarly, taking the address of an unaligned field also captures the entire struct
```
@repr(packed)
struct T(String, String);

mut t := T(String.new(), String.new());
c := fn {
    a ;= std.ptr.#addr_of(t.1); // captures `t` immutably
}
a := t.0; // error: cannot move out of `!t.0` because it is borrowed
c();
```
> _Todo_: is the path and syntax of `addr_of` correct?

But the above works if the struct is not packed, since it captures the field precisely
```
@repr(packed)
struct T(String, String);

mut t := T(String.new(), String.new());
c := fn {
    a ;= std.ptr.#addr_of(t.1); // captures `t.1` immutably
}
// The move is still allowed here
a := t.0;
c();
```

##### DerefMove implementations [↵](#capture-precision-)

> _Todo_: Figure out API and mechanics

#### Unique immutable borrows in captures[↵](#11123-closure-types-)

Captures can occur by a special kind of borrow called a _unique immutable borrow_, which cannot be used anywhere else in the language and cannot be written out explicitly.
It occurs when modifying the referent of a mutable reference, as in the following example: 
```
mut b := false;
x := &mut b;
mut c := fn {
    // An immtable and mutable borrow of `x`
    a := &x;
    *x = true; // `x` captured by unique immutable borrow
}
// The follwoig line is an error:
// y := &x;
c();

// However, the following is Ok.
z := &x;
```

In this case, borrowing `x` mutably is not possible, because `x` is not `mut`.
But at the same time, borrowing `x` immutably would make the assignment illegal, because a `& &mut` reference might not be unique, so it cannot safely be used to modify a value.
So a unique immutable borrow is used: it borrows `x` immutably, but like a mutable borrow, it must be unique.

In the above example, uncommenting the declaration of `y` will produce an error because it would violate the uniqueness of the closure's borrow of `x`.
The declaration of `z` is valid because the closure's lifetime has expired at the end of the block, releasing the borrow.

#### Call traits and coercions[↵](#11123-closure-types-)

Closure types implement `FnOnce`, indicating that they can be called once by consuming ownership of the closure.
Additionally, some closures implement more specific call traits:
- A closure which does not move out of any captured variables implements `FnMut`, indicating that it can be called by mutable references
- A closure which does not mutate or move out of any captured variables implements `Fn`, idnicating that it can be called by shared reference.

> _Note_: `move` closures may still implement `Fn` and `FnMut`, even though they capture variables by move.
>         This is because the traits implemented by a closure type are determined by what the closure does with captured values, not how it captures them.

Non-capturing closures are closures that don't capture anything from their environment.
These can be coerced to function pionters (e.g. `fn()`) with the matching signature

> _Todo_: Using rust-like trait names now, is this correct?

#### Drop order[↵](#11123-closure-types-)

If a closure captures a field of a composite type like a struct, tuple, or enum by value, the field's lifetime would not be tied to the closure.
As a result, it is possible for disjoint fields of a composite types to be dropped at different times.
``` 
{
    tup := ("foo":s, "bar":s); // ------------------+
    { //                                            |
        c := fn { // -----------------------------+ |
            // tup.0 is captures into the closure | |
            drop(tup.0); //                       | |
        }; //                                     | |
    } // 'c' and 'tuple.0' dropped here ----------+ |
} // tup.1 dropped here ----------------------------+
```

> _Todo_: types below here

### 11.1.25. Trait Object types [↵](#111-types-)

```
<trait-object-type> := 'dyn' <trait-bound>
```

A trait object type is an opaque type that implements a set of traits, any set of traits is allowed, except of an opt-in trait like `?Sized`.
The objects are guaranteed to not only implement the given traits, but also their parent traits.

Different trait objects may alias each other if the traits match, but are in different orders, meaning that `dyn A & B & C` is the same as `dyn C & A & B`

A trait object can be assigned to any less specific trait objects, meaning that it can be assgined to a type that has less trait bounds.
This *may* incur some additional overhead, as a new vtable needs to be retrieved and assigned, if this cannot be determined at compile time.

Due to the opaqueness of trait objects, this type is dynamically sized, meaning that it must be stored behind a reference, a pointer, or a type accepting DTSs.

Trait objects are stored in so-called "fat pointers' which consists out of 2 components:
- A pointer to the an object of a type `T` that implements the trait bounds
- A virtual table, also known as a vtable, which contains both RTTI info and a list of function pointers to the methods of the traits and their parent types, of `T`'s implementation.

Trait object types allowe for "late binding" in cases where the types being used cannot be known at compile time, but the programmer knowns the functionality they posses.
Calling a method will use a virtual dispatch of the method: that is, teh function pointer is loaded from the vtable, and is then invoked indirectly, incurring a pointer indirection.
The actual implementation of each vtable may vary on an object-by-object basis.

### 11.1.26. Impl trait types [↵](#111-types-)

```
<impl-trait-type> := 'impl' <trait-bound>
```

An impl trait type introduces an unnamed generic parameter that implements the given intrefaces to the item it is used in.
It can appear in only 2 locations: function paramters (where it acts as an anonymous type of the parameter to the function) and function return types (where it acts as an abstract return type).

#### Anonymous type parameter [↵](#11124-impl-trait-types-)

A function can use an impl trait type as the type of its parameter, where it declares the parameter to be of an anonymous type.
The caller must provide a type that statisfies the bounds declared in the anonymous type paramter, and the function can only use the functionality available through the trait bounds of the anonymous type paramter.

An example of this would be:
```
trait Trait {}

// Generic type parameter
fn with_generic_type<T is Trait>(param: T) {}

// impl trait typed paramter
fn with_impl_type(param: impl Trait) {}
```

This can be seens as synctactic sugar for a generic type paramter like `<T is Trait>`, except that the type is anonymous and does not appear within the generic argument list.

> _Note_: For function arguments, generic type parameters and `impl Trait` are not completely equivalent
> With a generic type paramter `<T is Trait>`, the caller is able to explicitly specify the type of the generic type parameter `T` when calling the function.
> If an `impl Trait` is used, the caller cannot ever specify the type of the parameter when calling the function.
>
> Therefore, changing between these types within a function signature should be considered a breaking change.

#### Abstract return types [↵](#11124-impl-trait-types-)

A function can use an impl trait type as the type in its return type.
These types stand in for another concrete type wher the caller may only used the functinality declared by the specified traits.
Each possible return type of the function must resolve to the same concrete type.

An `impl Trait` in the return allows to return a abstract type that does not have to be stored within dynamic memory.
This can be particularly usefull when writing a function returning a closure or iterator, as for example, a closure has an un-writable type.

Without this functionality, it would only be possible to return a 'boxed' type:
```
fn returns_closure() -> Box<todo> {
    Box::new(|x| x + 1)
}
```

This could incur performance panalties from heap allocation and dynamic dispatching.
However, using this type, it is possible to write it as:

```
fn returns-closure -> impl todo {
    |x| x + 1
}
```

Which avoids the drawbacks of the 'boxed' type.

_TODO: add note on (memory) effect implications_

#### Abstract return types in trait declarations [↵](#11124-impl-trait-types-)

Functions in traits may also return an abstract return types, this will create an anonymous associated type within the trait.

Evety `impl Trait` in the return type of an associated function in an trait is desugared to an anonymous associated type.
The return type that appears in teh implementation's funciton signature is used to determine the value of hte associated type.

##### Differences between generics and `impl Trait` in a return [↵](#abstract-return-types-in-trait-declarations-)

When used as a type argument, `impl trait` work similar to the semantics of generic type parameters.
But when used in the return, there are significant changes, as unlike with a generic parameter where the caller can choose the return type, the implementation chooses the function's return type.

For example, the function
```
fn foo<T is Trait>() -> T { ... }
```
Allows the caller to determine the return type.

In contrast, the function
```
fn foo() -> impl Trait { ... }
```
doesn't allow the caller to explicitly determine the return type.
Instead the function chooses the return type, with the only guarantee that it implements the required traits.

#### Impl trait limitations [↵](#11124-impl-trait-types-)

An impl trait type may only occur for non-`extern` functions.
It can also not be the type of a variable declaration, a field, or appear inside a type alias.

### 11.1.27. Inferred types [↵](#111-types-)

```
<inferred-type> := '_'
```

An inferred type tell the compiler to infer the type (if possible) based on the surrounding information available.
Inferred types cannot be used in generic arguments.

Inferred types are often used to let the compiler infer the type of generic parameters:
```
let a : DynArr(_) = ...;
```

### 11.1.28. Vector types [↵](#111-types-)
```
<vec-type> := '[<' <expr> '>]' <type>
```

A vector type represents a group of values that are operated on in parallel, using SIMD where possible.
Vectors consists out of multiple underlying types, each with a predetermined size.

They generally support the same core operators that their base types support.
Operations on vectors are generally done element-wise, but this is not guaranteed, check the relavent function for more info about this.

> _Note_: By default, only primitive types are supported, check `Vector`'s implementation for more details.

#### Custom vector support [↵](#11128-vector-types-)

Custom vector support can be added by specializing the `Vector` type, using:
```
#specialize_vector(ty, n) {
    ...
}
```
This both allows functionality to be implemented, and notifies the compiler of the newly supported type.

If needed the underlying implementation may also be replaced by specifying a type that matches that of the vector implementation, e.g.:
```
struct Vec(T is SupportedVecType, N: usize) where
    is_vec_size_supported(N)
{
    ...
}
```
If a custom vector type is used, the `#specialize_vector` metafunction will applied to that type.

An example of a specialized vector would be GPU vector support.

> _Note_: Defining a custom vector implementation requires the library with the implementation be provided to the compiler and the `std` lib must be compiled from scratch

> _Todo_: Is this the best way of doing this?

## 11.2. Dynamically sized types [↵](#11-type-system-)

Most types have a fixed size that is known at compile time and implements the `Sized` trait.
A type wit ha size tha is only known at compile-time is called a dynamically sized type (DST), or informally, unsized types.
Slices and trait objects are two such examples.

DSTs can only be used in certain cases:
- Pointers and references to DSTs are sized, but have twice the size of a pointer of a sized type.
    - Pointers to slices store the number of elements in the slice.
    - Pointers to trait objects store a pointer to their vtable.
- DSTs can be provided as type arguments to generic type parameters that have a special `?Sized` bound.
  They can also be used for associated type definitions when the corresponding associated type is declared using the `?Sized` bound.
  By default, any type parameter has a `Sized` bound, unless explicitly relaxed using `?Sized`
- Trait may be implemented for DSTs.
  Unlike with generic type paramters, `Self is ?Sized` is the default in trait definitions.
- Struct may contains a DST as the last field, this makes the struct itself a DST.

## 11.3. Nominal vs structural types [↵](#11-type-system-)

Minoa has types that can either be nominal or structural, between these 2 kinds of types.

Both have the same type layout and mutability rules, but there are some important differences:

Nominal types:
- Nominal types do **not** implicitly implement any traits.
- Nominal types can have additional functionality and traits implemented.
- All field have configurable visibility.
- The types can be accessed directly from other scopes when 'imported'.

Structural types:
- Structural types implicitly implement a set of traits, depending on the values of the members, these are:
    - `Clone`
    - `Copy`
    - `Equality`
    - `StructuralEquality`
    - `Identity`
    - `StructuralIdentity`
    - `Hash`
    - `Debug` _TODO: this will likely the be the trait, but depends on the standard format implementation._
- Structural types do not allow any additional functionality to be implemented, as they are strictly plain data types.
- Fields cannot have explicit visibility.
- The types only exist within the scope they are defined, unless publically aliased.

> _Note_: Structural equality means that the result of an equality would be the same as checking that of all fields individually.

## 11.4. Type layout [↵](#11-type-system-)

The layout of a type defines its size, alignment, and its internal representation of data/fields.
For enums, how their distriminant is laid out is also part of the layout.

Type layouts can change inbetween compilations.

### 11.4.1. Size and Alignment [↵](#114-type-layout-)

All values have a size and alignment.

The alignment of a value specifies at what boundaries in memory the value can be stored.
A type with alignment `N` must be stored at an address that is a multiple of `N`.
Alignment is measured in bytes, is at least 1, and is a power of 2.

The size of a value specifies the offset that is needed to be able to place the next value, e.g. the offset of the subsequent element in an array.
The size will always be a multiple of its alignment, guruaranteeing that any subsequent value of this type will be correctly aligned by default.

Is it possible for a given type to be a zero-sized type, as a size of 0 is a valid multiple of its alignment.
On some platforms, a zero-sized types might still be required to follow a specific alignment, e.g. in the case of `[0]i32`, the value needs to be aligned to `4`.

the majority of types will know their size and alignment at compile time, these are called 'sized types'.
Sized types can have their size and alignment checked at compile time.
Meanwhile types that are not known at compile time, as known as [dynamically sized types](#112-dynamically-sized-types).

Since all values of a sized types share their size and alignment, we say that they have the type's size and alignment.

### 11.4.2. Primitive layout [↵](#114-type-layout-)

The size of most primitive types can be found in the table below:

Types                                                | Size/Alignment (bytes) | Size in bitfield (bits) | Alignment in bitfield (bits)
-----------------------------------------------------|------------------------|-------------------------|------------------------------
`i8`   / `u8`            / `b8`  / `char8`           | 1                      | 8                       | 8
`i16`  / `u16`  / `f16`  / `b16` / `char16`          | 2                      | 16                      | 16
`i32`  / `i32`  / `f32`  / `b32` / `char32` / `char` | 4                      | 32                      | 32
`i64`  / `i64`  / `f64`  / `b64`                     | 8                      | 64                      | 64
`f80`                                                | 10 / 2                 | 80                      | 16
`i128` / `u128` / `f128`                             | 16                     | 128                     | 128
`usize` / `isize`                                    | see below              | see below               | see below
`bool`                                               | 8                      | 1                       | 1
`char7`                                              | 1                      | 7                       | 1

`usize` and `isize` are different to other types, as they contain types that fit the entire memory address space of the target platform.
For example, on a 32-bit system, this is 4, and on an 64-bit system, this is 8.
These sized also often match up with that of the target register size, but this cannot be guaranteed.

The alignment of types is generally platform-specific, but to keep this consistent across architectures, Minoa has diced to make these the same as their size.

When used in a bitfield, some primitive types may have different sizes and alignment to fit more tightly into memory.

### 11.4.3. Unit and never type layout [↵](#114-type-layout-)

Unit and never types are both 0-sized types with an alignment of 1.

### 11.4.4. Pointer and reference layout [↵](#114-type-layout-)

Pointers and references have the same layout.
The mutabilty of a pointer or reference has not impact on the layout.

Pointers and references to sized tyes are the same as those of a `usize`.

Pointers and references to usized types are typed. Their size and alignement is guaranteed to be at least eqal to the size of a `usize` and have the same alignment.

> _Note_: Currently all pointers and references to DST are twice the size of a `usize` and have the same alignment.
> Although this should not be relied on.

### 11.4.5. Array layout [↵](#114-type-layout-)

An array of the form `[N]T` has a size that is `N` times that of the size of type `T` and has the same alignment as type `T`.
Arrays are laid out so that the zero-based `n`th element of the array is offset from the start of the array by `n` times the size of type `T`.

When an array is sentinal terminated, the array contains an additional element of type `T` at the end, so the size of the array will be `N + 1` times the size of type `T`.

### 11.4.6. Slice layout [↵](#114-type-layout-)

Slices have the same alyout as a section of an array

> _Note_: This is about the ray `[]T` type, not pointers to arrays to slices, e.g. (`&[N]T`)

### 11.4.7. String slice layout [↵](#114-type-layout-)

A string slice's layout depends on the type of string slice, but they have the same representation as their internal slice layout.

Below is a table of string slices that have a corresponding type layout to the following slice types

String slice | Slice
-------------|-------
`str`        | `[u8]`
`str7`       | `[char7]`
`str8`       | `[char8]`
`str16`      | `[char16]`
`str32`      | `[char32]`
`cstr`       | `[char8]`

### 11.4.8. Tuple layout [↵](#114-type-layout-)

Tuples are laid out as defined in the [Minoa representation]().

### 11.4.9. Trait object layout [↵](#114-type-layout-)

Trait objects have the same layout as the value the trait that implements it.

> _Note_: THis is for the trait object itself, not a type containing the object, such as a reference.

### 11.4.10. Closure layout [↵](#114-type-layout-)

A closure has no layout guarantees.

### 11.4.11. Bitfield layout [↵](#114-type-layout-)

A bitfield will have the size and alignment of the smallest primitive types that fits the contents of the bitfield.

### 11.4.12. Layout representation [↵](#114-type-layout-)

All user-defined composite types have a representation that specifies how the type is laid out.
The possible representations for these are:
- `Minoa`
- `C`
- `soa`
- primitive type
- `transparent`

While the representation of a type can affect the padding between fields, it does not change the layout of the fields themselves.
If a composite type contains a field that had another layout already defined, that field will still use its own layout representation, and will not use the layout representation of the type containing it.

#### ... representation [↵](#11412)

The `...` represention is the default representation for nominal types without a `repr` attribute.
If this representation is explicitly specified by using the `repr` attribute, it will result in the same layout as if it was not explicitly defined.

This represetnation makes a mininal amount of guarantees about the layout of fields, but does guarantee the following:
- Each field is properly aligned
- Fields do not overlap
- The alignment of the type is at least that of teh field with the highest alignment.

The first guarante means that the offset of a field will always be a multiple of its alignment.
The second guarantee means that the fields can be ordered such that the offset plus the size of any field is less than or equal to the offset of the next field in the type.
This does not mean that zero-sized fields will have a unique offset and multiple zero-sized fields may be located at the same address.
The third guarantee ensures alignment of the all fields can always be guaranteed.

There is no guarantee that the ordering is the same as the one defined within code.

##### Field priority [↵](#11412-layout-representation-)

Since by default there is no guarantee on the ordering of fields, the type may lay out fields in such a way that they may not be optimally laid out for some usecases.
To ensure the programmer can provide additional hints to the compiler which fields should be prioritized during layout to ensure better caching of the type,
a field priority propery may be defined per field.

The priority takes on a value in the range of 0..=15, with 0 being the default for all fields.
Fields with a higher priority will be prefered to be laid out first in the type.

```
struct Foo {
    big: [256]u8,
    // Ensure the compiler lays out the fields in such a way that important will be most likely to be on a cache line
    @[field_priority(15)]
    important: u32,
}
```

#### C representation [↵](#11412-layout-representation-)

The C representation has 2 purposes:
- creating types that are interoperable with C libraries/code.
- allow types to be laid out in such a way that the layout of the type can be relied on.

This representation can be applied to `struct`s, `enum`s, and `union`s.

The C representation also affects the alignment of primitive types for the current target architecture.

##### `repr(C)` structs and records [↵](#c-representation-)

The alignment of a struct will be that of the most-aligned field.

The size of the type, and the size and offset of the fields will be determined uisng the method described below.

The current offset start at 0, then for each field within a type:
1. determine the size and alignment of the field
2. if the current offset is not a multiple of the field's alignment, set the current offset to the next multiple of the field's alignment. This space is padding.
3. the current offset will now become the offset for the field
4. increment the current offset by the size of the field.

> _Note_: This algorithm can produce zero-sized structs.
> While this is generally considered to be illegal in C, some compiler support option to enable zero-sized structs.
> Meanwhile C++ gives empty structures a size of 1, unless the are inherited or have fields using the `[[no_unique_address]]` attribute,
> in which case they do not contribute to the size of the overall struct.

##### `repr(C)` unions [↵](#c-representation-)

A union with a C representaton has the same layout as the union would have if it were defined in C for the target platform.

The union will have the size of the largest fields in the union, and the alignment of the most-aligned field in the union.
These values may be taken from different fields.

##### `repr(C)` field-less enums and enum records, and flags enums [↵](#c-representation-)

When an enum is field-less, the C representation has the size and alignment of the default `enum` size for the target platform's C ABI.

> _Note_: The enum representation in C is implementation defined, so this is really a "best guess".
> In particular, this may be incorrect when the C code of interst is compile with certain flags
> If a known enum size is required, use a primitive represention.

##### `repr(C)` enums and enum records with fields [↵](#c-representation-)

The representation of an enum with fields is defined a `repr(C)` structure with 2 fields, these being:
- a `repr(C)` version of the enum with all field removed, i.e. the "tag"
- a `repr(C)` union of `repr(C)` records for the field of each variant that had them, i.e. the `payload`

#### Primitive representation

A primtiive representation is only allowed for `enum` values that have at least 1 variant, and on bitfields.

The allowed primitive types are `u8`, `u16`, `u32`, `u64`, `u128`, `usize`, `ui`, `i16`, `i32`, `i64`, `i128`, and `isize`.
When defining an enum with a primitive representation, an enum will use this type as its descriminant.

If an enum has no fields, the resulting enum will have the same size and aligment as the primitive type it is represented by.

When an enum has fields, it will be represented as a `repr(C)` enum, with its payload using the `repr(C)` representation.
In addition to the primitive representation, a second (non-primitive) representation may be provided, affecting the layout of the payload of the enum.

#### Transparent representation [↵](#11412-layout-representation-)

The transparent representation is only supported for structures and enum with only 1 variant, which have the following:
- a single field with a non-zero size
- any number of field with a 0-sized type and alignment 1

Type using this representation have the same lyout and ABI as the single non-zero field.

Unlike other representations, a type with this represetnation takes on that of the underlying non-zero sized type.

#### SAO (structure of array) representation [↵](#11412-layout-representation-)
_TODO_

#### Additional layout modifiers [↵](#11412-layout-representation-)

The `repr` attribute may also optionally contain an `align` or `packed` value, these can be used to raise or lower the alignment respectively.
On their own, neither provide guarantees about the ordering of any fields in the layout of the type, although they may be combined with representations such as 'C', which do provide such guarantees.

Either modifier may be applied to structs, unions, and records.
In addtion, `align` may also be applied to enums and enum records.

The alignment specified by either the `align` of `packed` attributes must be a power of 2 from 1 up to 2^32.
For `packed`, if no explicit alignment is given, this will default to 1.

The `align` modifier changes the minimum alignment for the type, if the value given is lower than the actual alignment of the type, the alignemnt is unaffected, otherwise, it will increase the alignment to the given value.

The `packed` modifies affect the alignment of each fields within the type, but does not chang the alignment of the layout of the fields themselves.
If this alignment is larger than the alignment of the type, the offset of fields are unaffected.
Otherwise the offset of fields is affect, as this modifier affects the minimal required alignment of fields that is decided by the current representation, i.e. fields will be aligned to the alignment provided to the attribute.

Only one of the `align` or `packed` modifiers can be applied to a type at any type, and it may only be applied to types with either a `Minoa` or `C` representation.

> _Todo_: move info to separate-categories

#### `align`

#### `packed`

> _Todo_: make clear that `packed` works on a byte boundary, and to use `bitfield` for packing in bits

## 11.5. Interior mutability [↵](#11-type-system-)

Sometimes a type needs to be mutated while having multiple aliases.
This can be achieved using a concept called _interior mutability_.
A type has interior mutability if its internal state can be modified from a shared reference to it.
This goes against the usual requirement that the value pointed to by a shared reference is not mutated.

`UnsafeCell<T>` is the only way of disabling this requirement.
When `UnsafeCell<T>` is immutably aliased, it is still safe to mutate or obtain a mutable reference to the `T` it contains.
As with all other types, it is undefined behavior to have multiple `&mut UnsafeCell<T>` aliases.

Other types with interior mutabiliity can be created using `UnsafeCell<T>` as a field.

> **Warning**: The programmer must ensure that this does not cause any unininted consequences or may cause other undefined behavior.

## 11.6. Type coercions [↵](#11-type-system-)

Type coercions are implicit operations that change the type of a value.
They happen automatically at specific locations and are highly restricted in what types are allowed to coerce.

Any conversions allowed by coercion can als obe explicitly performed using the type cast operator `as`.

> _Note_: This description is informal and not yet fully defined, and should be more specific

### 11.6.1. Coercion sites [↵](#116-type-coercions-)

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


### 11.6.2. Coecion types [↵](#116-type-coercions-)

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

### 11.6.3. Unsized coercions [↵](#116-type-coercions-)

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

### 11.6.4. Least upper bound coercions [↵](#116-type-coercions-)

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


## 11.7. Destructors [↵](#11-type-system-)

When an initialized variable or temporary goes out of scope, its destructor is run, or it is _dropped_ (this terminology is taken from rust).
Assignment also runs the destructor of its left-hand operatnd, if it's initialized.
If a variable has been partially initialized, only its initialized fields are dropped.

The destructor of a type `T` consists out of:
1. If `T is Drop`, calling `<T as Drop>::drop`, or
2. Recursively running the destructor of all its fields
    - The fields of a struct or record are dropped in declaration order
    - The fields of the active enum or enum record variant are dropped in declaration order
    - The fields of a tuple are dropped in order
    - The elements of an array or owned slice are dropped from the first element to the last.
    - The variables that a closure captures by move are dropped in an unspecified order
    - Trait objects run the destructor of the underlying type
    - Other types don't result in any further drops

If a destructor must be run manually, such as when implementing a smart pointer, `drop_in_place` can be used.

### 11.7.1. Drop scopes [↵](#117-destructors-)

Each variable or temporary is associated with a drop scope.
When control flow leaves a drop scope, all variables associated to that scope are dropped in reverse order of declaration (for varialbes) or creation (for temporaries).
Values are only dropped after running all `defer` statements within the same scope.

Drop scopes are determined after replacing `for`, `if` and `while` expressions (with let bindings) with the equivalent using `match`.
Overloaded or user-defined operators are not distinguished from built-in operators and binding modes are not considered.

Given a function, or closure, there aer drop scopes for:
- The entire function
- Each statement
- Each expression
- Each block, including the function body
    - In the case of block expressions, the scope for the block and the expressions are the same scope.
- Each arm of the `match` expression

Drop scopes are nested within each other as follows.
When multiple scopes are left at once, such as when returning from a function, variables are dropped from the inside outwards.
- The entire function scope is the outer scope
- The functon body block is contained within the scope of the entire function.
- The parent of the expression is an expression statement is the scope of the statement.
- The parent of the expression of a variable declaration is the declaration's scope.
- The parent of the statement is the scope of the block that contains the statement.
- The parent of the expression for a `match` guard is the scope of the arm that the guard is for.
- The parent of the experssion after the `=>` in a `match` is the scope of the arm it's in.
- The parent of the arm scope is the scope of the `match` expression that it belongs to.
- The parent of all other scopes is the cope of hte immediately enclosing expression.

### 11.7.2.  Scopes of function parameters [↵](#117-destructors-)

All function paramters are in the scope of the entire function, so are dropped last when evaluating the function.
Each actual function parameter is dropped after any bindings introduced in that parameter's pattern.

> _Todo_: Example

### 11.7.3. Scopes of local variables [↵](#117-destructors-)

Local variables declared in a variable declaration are associated to the scope that contains the declaration.
Local variables declared in a `match` expression are associated to the arm scope of the `match` that they are declared in.

> _Todo_: Example

If multiple patterns are used in the same arm of a `match` expressions, then an unspecified pattern will be used to determin the drop order.

### 11.7.4. Temporary scopes [↵](#117-destructors-)

The temporary scope of an expressions is the scope that is used for the temporary variable that holds the result of he exprssion when used in a place context, unless it is promoted.

Apart from lifetime extensions, the temprory scope of an expression is the smallest scoped that contins the expression and is one of the following:
- The entire function.
- A statement.
- The body of an `if`, `while` or `loop` expression.
- The `else` block.
- The condition expressions of an `if` or `while` expression, or a `match` guard.
- The body expression for a `match` arm.
- The second operand of a lazy boolean operator.

> _Note_: Temporaries that are created in the final exprssion of a function body are dropped after any named variables bound in the function body.
> Their drop scope is the entire function, as tehre is no smaller enclosing temporary scope.
>
> The scrutinee of a `match` expression is not a temporary scope, so temporaries in the scrutinee can be dropped after the `match` expression.
> For example, the temporary for `1` in `match 1 { ref mut z => z };` lives until the end of the statement.

_TODO: Example_


### 11.7.5. Operands [↵](#117-destructors-)

Temporaries are also created to hold the result of operands to an expressions while the other operands are evaluated.
The temporaries are associated to the scope of the expressions with that operand.
Since the temporaries are moved from once the expreesssion is evaluated, dropping them has no effect unless one of the operands to an expression break out of he expression, returns, or panics.

_TODO: Example_

### 11.7.6. Constant promotion [↵](#117-destructors-)

Promotion of a value expression to a `static` slot occurs when the expression could be written in a constant and borowed, and that borrow could be dereferenced where the exprssion was originally written, without changing the runtime behavior.
That is, the promoted expression can be evaluated at compile-time and the resulting value does not contain [interior mutability](#115-interior-mutability-) or [destructors](#117-destructors-) (these properties are determined based on the value when possible).

### 11.7.7. Temporary lifetime extension [↵](#117-destructors-)

> _Note_: This is subject to change

The temporary scopes for expressions in variable declarations are sometimes extended to the scope of the block containing the declaration.
This is done wherer the usual temporary scope would be too small, based on syntactic rules.

If a borrow, dereference, field, or tuple expression has an extended temporary scope, the nteh indexed experssions also has an extended scope.

### 11.7.8. Extending based on patterns [↵](#117-destructors-)

An extending pattern is either:
- An identifier pattern that binds by refernce or mutable reference.
- A struct, tuple, tuple struct, or slice pattern where at least one of the direct subpatterns in an extending pattern.

So `ref x`, `V(ref x)` and `[ref x, y]` are all extending patterns, but `x`, `&x` and `&(ref x, _)` are not.

If the pattern in a variable declaration is an extending pattern, then the temporary scope of the initializer expression is extended.

### 11.7.9. Extending based on expressions [↵](#117-destructors-)

For a variable declaration with an initializer, an extending expression is an experssion whici is one of the following:
- The initializer expression.
- The operand of an extending borrow experssion.
- The operand of an extending array, cast, braced struct, or tuple expression.
- The final expression of any extending block expression.

So the borrow expression is `&mut 0`, `(&1, &mut 2)`, and `Some{ 0: &mut 3 }` are all extending expressions.
The borrows in `&0 + &1` and `Some(&mut 0)` are not: the latter is syntactically a function call expression.

The operand of any extending expression has its temporary scope extended.

### 11.7.10. Not running destructors [↵](#117-destructors-)

`forget` can be used to prevent the destructor of a variable from being run, `ManuallyDrop` provides a wrapper to prevent a variable or field from being dropped automatically.

> _Note_: Preventing a destructor from being run via `forget` or other means is safe even if the type isn't static.
> Besides the place where destructors are guaranteed to run as defined by this document, types may not safely rely on a destructor being run for soundness.

# 12. Generics [↵](#tables-of-contents)

Generics allows for code reuse by writing generalized versions of items and types that can take in compile time constants.
Generics are compile-time arguments the compiler can use for this.

While types themselves are values, there are some slight differences between type and (other) value generics, as specified below:
- type generics have an implicit bound to `Sized`, and need an explicit optional or negative bound to relax this implicit bound.
- value generics require to be either a builtin type, or a type adhering to the `StructuralIdentity` trait

The act of creating a version of a generic item, is called instanciation or monomorphization.

Type parameters also allow bounds to be specified at their declaration site, this is syntactic sugar for the same bound in a where clause.

## 12.1. Generic parameters [↵](#12-generics-)
```
<generic-params>        := '[' <generic-param> { ',' <generic-param> }* [ ',' ] ']'
<generic-param>         := <name> [ <generic-param-type> ] [ '=' <expr> ]
<generic-param-type>    := ':' <type>
                         | 'is' <trait-bounds>
```

Generic parameter allow the adding of generics to items.

If no explicit type is provided for a generic parameter, `type` will be assumed

### 12.1.1. Optional parameters [↵](#121-generic-parameters-)

Generic parameters may be provided with a default value for when no explicit value is given when instantiating a generic value, these will then be optional generic parameters.
Similarly to optional function parameters, they must appear after all non-optional (also known as required) generic parameters.

Unlike in functions, since generic parameters don't have label, generic arguments must come in the same order as its corresponding parameters.

## 12.2. Generics in functions [↵](#12-generics-)

Generics within a function is handled by the compile-time arguments passed into a function.

These arguments can be used just like any generic parameter, but can also be used to precompute values withing code.

When a generic function is passed into a place that expects a closure, the function can 'lowered' to bake in the generic arguments, using an `...` (elipsis) expression.

For example, if we have the following generic function
```
fn foo(:_ const T: type, :_ a: T) { ... }
```

and some code which can take in a function
```
fn bar(foo: fn(i32)) { .. }
// or
fn bar(const T: type, foo: T) where T is Fn(i32) { ... }
```

Then `foo` can be converted to a matching function
```
bar(foo: foo(i32, ...))
// or
bar(foo: foo(_, ...))
```

## 12.3. Parameter packs [↵](#12-generics-)
```
<parameter-pack>  := <param-pack-name> '...' [ ':' <param-pack-desc> ] [ '=' <expr> { ',' <expr> } [ ',' ] ]
<param-pack-name> := <name>
                   | '(' <name> { ',' <name> } [ ',' ] ')'
<param-pack-desc> := <type>
                   | 'is' <generic-bound>
```

A parameter pack is an array of 0 or more instances of a generic parameter set.
Parameter packs therefore allow for an unknown amount of generic arguments to be passed to an item.
Parameter packs are only allowed as the last parameters withing a generic parameter declaration.

A parameter set is a collection of names/typed provided to the generic item.
A set may consist out of either a single names element, or a destructured group of multiple sub-elements.

When providing values, for parameter set, all elements need to be passed, meaning that the total amount of arguments passed as generic arguments needs to be an integer multiple of the number of names defined within the variadic parameter's definition.


Like any other generic parameters, the parameter pack can be given a name.
But unline other parameters, if a parameter set contains multiple element, they can be destructured into muliple sub-packs.
The number of sub-packs must be equal to the number of elements within a parameter pack's description.

If no parameter pack description is provided, all sub-packs will be defaulted to `type`.
If a sub-pack is specified as `is ...`, then this will also be of type `type`, but with the constraints specified applied to it.
The description also specifies the group size, i.e. how many elements are in each occurance of the pack.

A parameter pack may also be optional, by provided a default value, the number of default values must consists of an integer mulitple of the description's group size, and must be compatible with the type in the description.

> _Note_: This is related to [variadic parameters](#variadic-parameters-) in functions

> _Todo_: Figure out ergonimics, i.e. number of params, looping over them, etc. A set of metafunction will be used for this, as they are a SOA of values

## 12.4. Where clauses [↵](#12-generics-)
```
<where-clause> := 'where' <expr> { ',' <expr> }
<generic-bound> := <expr>
                 |
```

A where clause is a collection of bounds that the generic parameters need to adhere to.
This contains all bounds generic arguments must adhere to when creating an instance of the item.

Bounds are comma separated list of boolean expression determining if the given value adheres to it

Since type are values, they can be handled using any expression, similar to how values can be bound.

## 12.5. Trait bounds [↵](#12-generics-)
```
<trait-bounds>       := <path> { '&' <path> }
<trait-bound>        := <path> [ '(' <trait-assoc-bounds> ')' ]
                      | '?' <path>
                      | '!' <path>
<trait-assoc-bounds> := '.' <name> 'is' <type-bound>
                      | <return-type-trait-bound>
```

A trait bound is used to check if a given type adheres to a set of trait.

Trait can be checked in 3 different ways:
- Positive bounds: this requires a type to implement the given bound
- Optional bounds: this allows a type to possibly implement a type, this is required if any specialization on a type based on whether this trait is implemented or not.
  In case of `Sized`, relaxes the default bound and allows a type to have a dynamic size.
- Negative bounds: this requires that a type does __not__ implement a given type.

## 12.6. Return type bounds
```
<return-type-bound>       := <path> '(' '..' ')' 'is' <trait-bounds>
<return-type-trait-bound> := '.' <name> '(' '..' ')' 'is' <trait-bounds>
```

Return type bound allow bound to be applied on the return types of a function or method, that is associated with one of the generic types.

## 12.6. Constraints [↵](#12-generics-)
```
<constraint-item>   := { <attribute>* } [ <vis> ] 'constraint' <name> [ <generic-params> ] '{' <constraint-members> { ',' <constraint-member> } [ ',' ] '}'
<constraint-member> := <trait-item>
                     | <expr>
```

A constraint is similar to a trait, except that it does not need to be explicitly implemented.
In addition, a constraint may also contain generic bounds in addition to the body.

Constraint items may only act on directly associated items, meaning associated items directly implemented (i.e. without an trait), and [extended trait items]().

> _Todo_: Link to extended trait item implementations

### 12.7.1. Constraint functions & methods [↵](#126-constraints-)
```
<constraint-fn>        := [ 'unsafe' ] [ 'const' ] 'fn' <name> [ <generic-params> ] '(' <constraint-fn-params> ')' [ '->' <fn-return> ] [ <where-clause> ] ';'
<constraint-fn-params> := <constraint-fn-param> { ',' <constraint-fn-param> } [ ',' ]
<constraint-fn-param>  := <name> ':' <type>

<constraint-method>    := [ 'unsafe' ] [ 'const' ] 'fn' '(' <receiver> ')' <name> '(' <constraint-fn-params> ')' [ '->' <fn-return> ] [ <where-clause> ] ';'
```

Constraint functions and methods are very similar to [trait functions & methods](#736-trait-functions--methods-).

But they use a simplified set of parameters and may not contain:
- attributes
- a body
They require that any type has a function or method matching the signature of the constraint function or method.

A constraint function's parameter exist out of 2 things:
- a name corresponding to the label of that variable (may include keywords), and
- the type of the argument

If a function contains an parameter with `N` names, it needs to be expressed by `N` parameters in the constraint function.

### 12.7.2. Constraint type aliases [↵](#126-constraints-)
```
<constraint-type-alias> := 'type' <name> [ <generic-params> ] [ <where-clause> ] ';'
```

Constraint type aliases are very similar to [trait type aliases](#743-trait-type-aliases), but cannot have a default value.
They require that any type has a type alias with a matching name and type.

### 12.7.2. Constraint constants [↵](#126-constraints-)
```
<constraint-const> := 'const' <name> ':' <type> ';'
```

Constraint constants are very similar to [trait constants](#791-trait-constant-), but cannot have a default value.
They require that any type has a constant with a matching name and type.

### 12.7.3. Contraint properties [↵](#126-constraints-)
```
<constraint-prop>        := 'property' ( <name> | <int-literal> ) ':' <type> '{' <constraint-prop-member> '}'
<constraint-prop-member> := [ [ 'mut' ] 'ref' ] 'get' ';'
                          | 'set' ';'
```

Constaint properties are very similar to [trait properties](#7113-trait-properties-), but cannot have default implementations.
The requires that any type has either a property of field matching the name and type.

When a property has an integer literal instead of a name, this is associated with a tuple index field withing the constained type.

### 12.7.4. Constraint disambiguation aliases [↵](#126-constraints-)
```
<constrait-disambig-alias> := 'alias' <name> '=' <trait-path> '.' <name> ';'
```

A constraint disambiguation alias is an alternate name given to an item in a trait, which can be used as a disambiguation in a path.

For example: `alias do_foo = Foo.do_something` allows the following 2 call to essentially be the same thing:
```
value.(Foo.do_something)();
value.do_foo();
```

## 12.7. Generic arguments [↵](#12-generics-)
```
<generic-args> := '(' <expr> { ',' <expr> }* [ ',' ] ')'
```

Generic arguments are the same as normal constant function arguments, except that they can also be used on non-function items.

> _Note_: Within functions, these are interleaved with runtime arguments

## 12.8. specialization [↵](#12-generics-)

Specialization allows a different generic item to be generated depending on the arguments passed.
These are split up in 2 kinds:

### 12.8.1. Internal specialization [↵](#128-specialization-)

Internal specialization can only happen in a function, and is done using branches on a commpile-time conditional within a function.

For example:
```
fn foo(const T: type) {
    if T == i32 {
        // Do something
    } else {
        // Do something else
    }
}
```

### 12.8.2. External specialization [↵](#128-specialization-)

External specialziation is a mechanism that allows specialization of trait implementation on types.
This is done by passing an explicit expression that does not include on of the `impl`'s generic parameters.

> _Note_: Specialization may only occur on trait implementations, any other specialization needs to be done using internal specialization

For example:
```
// Base case
impl(T) A(T) as Foo {
    ...
}

// Specialization
impl A(i32) as Foo {

}
```

#### Collisions [↵](#1282-external-specialization-)

A collision occurs when neither implementation is more specific then another, meaning that 2 or more differently bound specializations may be valid for a single type.

Since we have the orphin rule for traits, we can be certain that any possible collision will be limited to within the current library.

Collisions can happen on trait bounds, such as independent type bounds where 2 or more bounds that aren't each other base traits, for example
```
trait Foo(T) {
    fn foo();
}

struct A;
impl(T is Bar) A as Foo(T) {
    fn foo() { ... }
}
impl(T is Baz) A as Foo(T) {
    fn foo() { ... }
}

struct B;
impl B as Bar;
impl B as Baz;

// Variant cannot be resolved here, the `B` generic argument is a valid type for either `foo` implementations
a := A;
a.(Foo(B).foo)();
```

Or they can happen on any expression bound:
```
trait Foo(N: usize) {
    fn foo();
}

struct A;
impl(N:usize) A as Foo(N) where N > 0 { ... }
impl(N:usize) A as Foo(N) where N < 2 { ... }

a := A;
a.(Foo(B).foo)();
```

#### Resolving Collisions [↵](#1282-external-specialization-)

Collision can be resolved usign the `spec_priority` attribute, which allows an integer value to be used to decide what specialization should be preferred during specialization.
A higher value means that the specialization will be prefered over any other specialization with a lower value. By default, the priority is set to 0.

The above example using trait bounds can be fixed in the following way
```
...

// Now this will be picked over the implementation below it
@spec_priority(1)
impl(T is Bar) A as Foo(T) {
    fn foo() { ... }
}

impl(T is Baz) A as Foo(T) {
    fn foo() { ... }
}

...

```
### 12.9. Restrictions [↵](#12-generics-)

As specialization may be implement on top of any type, this may lead to the possibility of having either recursive or infinitly expanding monomorphization.
To prevent this, any specialization has 2 limits placed on in:
- Specializations may not be recursive, meaning that a specialization may not rely on the exact same specialization being resolved
- Specializations may not widen a type

#### Recursive dependencies

Recursive dependencies are caused if a specialization for `A` requires a specialization of `B`, but `B` also requires one for `A`.

For example:
```
impl(T) A(B(T)) as Foo where B(T) is Foo {}
impl(T) B(A(T)) as Foo where A(T) is Foo {} 
```

#### Widening types

Types are widened, when a type in specialization is constrained by a type, which itself contains the type it is contraining.

For example
```
impl(T) T where T is ^T {

}
```

In this case, we are specializating on `T` by requiring it to adhere to being a version of `^T`.
The issue is that `^T` itself contains `T`, which would cause a run-away type check, as specializing for `T`, requires a specialization for `^T` to exists, which in turn requires one for `^^T`, etc.

Below are some more examples which would cause this issue:
```
impl(T) DynArr<T> where T is DynArr<T> {}
impl(T) DynArr<T> where T is []T {}
```

# 13. Metaprogramming [↵](#tables-of-contents)
```
<meta-decl>       := 'meta' 'fn' <name> [ <deduced-params> ] [ <meta-signature> ] '->' <meta-return> <block>
                   | 'meta' 'fn' <name> <meta-pattern-match-body>
<meta-signature>  := <meta-pattern-parse-signature>
                   | '(' <name> ':' ? Token slice or AST type ? ')'
                   | '()'
<meta-return>     := 'type'
                   | ? Tokens or AST type ?

<meta-expr>       := '#' <name> [ <meta-invocation> ]
<meta-invocation> := '(' <tokens> ')'
                   | '{' <tokens> '}'
                   | '[' <tokens> ']'
```

Meta-functions are special function-like items which allow for the compile-time generation of code.
Like regular functions, meta-function can be passed induced arguments and type arguments.
But unlike regular functions, meta-functions can return type created within the function.

In addition to the data passed to the meta-function, the meta-function can also use the provided info passed in the implicit context.

Meta-function can be defined within the library they are declared it, and can use any function available within that library.
However, a major restriction is that the code it relies on may **not** have any circular reliance on the current meta-function.

> _Note_: Meta-function must be defined directly within a module and may not be defined inside of any other item.

> _Todo_: Is the API correct for this entire section?

## 13.1. Meta variable [↵](#13-metaprogramming-)

A meta variable takes in no arguments and just returns some generated code.

Meta variables can both be called with or without subsequent meta invocation (i.e. delimiters).

In addition, if a meta-function takes in parameters, but all have default value defined, the function can be called as a meta-variable and will be provided with the default values.

For example:
```
meta fn meta_var() {
    ...
}
```
Can be called as either `#meta_var()` or `#meta_var`

## 13.2. Regular meta functions [↵](#13-metaprogramming-)

A regular meta-function acts like a normal function, taking in data directly from the compiler.

Although a meta-function may take any kind of input, there are 3 special signatures that have a different effect.

The special signatures are:
- `(tokens: meta.TokenStream)`: Provides the raw tokens given by the meta-function invocation
- `(partial_ast: meta.PartialAst)`: Provides a combination of AST fragments and tokens
- `(ast: meta.Ast)`: Provides a pre-parsed AST of the tokens given by the meta-function invocation

> _Note_: only the types need to match for the special signatures, the names don't matter

For example
```
use minoa:meta;

meta fn foo(tokens: meta.TokenStream) -> ... {
    ...
}

meta fn bar(ast: meta.Ast) -> ... {
    ...
}
```

## 13.3. Meta patterns [↵](#13-metaprogramming-)
```
<meta-pattern>           := { <token> | <meta-pattern-variable> }+
<meta-pattern-variable>  := '$' <name> : <meta-token-type>
                         | '$' '(' <meta-pattern-signature> ')' <macro-rep>
<meta-pattern-type>      := 'item'
                         | 'block'
                         | 'stmt'
                         | 'pat'
                         | 'expr'
                         | 'ty'
                         | 'iden'
                         | 'path'
                         | 'meta'
                         | 'vis'
                         | 'lit'
                         | 'toks'
<meta-pattern-rep>       := '?' | '*' | '+'
```

Meta patterns can be used to extract AST fragments form a given set of token inputs.T
They can be thought of as syntactic sugar utilizing some of the builtin meta-functions.

Meta-functions taking in meta patterns will have a signature that either takes in a `meta.TokenStream` or a `meta.FragmentStream`.


A meta function allows the function to take in a set of pre-parsed AST elements.
In addtion, `toks` can be used to supply a delimited set of raw tokens within the pattern (including the open and closing delimiters).

Each pattern type is syntactic sugar for the corresponding AST node, each corresponding to the following type, assuming `use minoa:meta;`:
pattern kind | fragment type         | corresponding language 'item'
-------------|-----------------------|-------------------------------
`item`       | `meta.ast.Item`       | an [item](#7-items-)
`block`      | `meta.ast.Block`      | a [block](#95-block-expression--)
`stmt`       | `meta.ast.Stmt`       | a [statement](#8-statements-)
`pat`        | `meta.ast.Pattern`    | a [pattern](#10-patterns-)
`expr`       | `meta.ast.Expr`       | an [expression](#9-expressions-)
`ty`         | `meta.ast.Type`       | a [type](#111-types-)
`name`       | `meta.ast.Identifier` | a [name](#33-names-)
`path`       | `meta.ast.Path`       | a [path](#52-paths-)
`meta`       | `meta.ast.Meta`       | an [attribute](#17-attributes-)
`vis`        | `meta.ast.Vis`        | a [visibility](#16-visibility-)
`lit`        | `meta.ast.Literal`    | a [literals](#6-literals-)
`toks`       | `meta.TokenStream`    | Any lexical [token](#3-lexical-structure-)

> _Note_: The fragments themselves are wrapped in type `meta.Fragment(T)`, where `T` is the fragment type


WHen using a special group pattern, indicated by a sub-pattern surrounded by `${}`, the amount of repetition are decided by the character following it.
This is one of the following
char | meaning
-----|-------------------------
`?`  | zero or one occurance
`*`  | zero or more occurances
`+`  | one or mor occurances

Group pattern are represented using a corresponding `meta.AstList(T)` type.


When matching a pattern, no lookahead is performed.
Meaning that patterns may be ambiguous, in this case, it will result in an error.
An example of an ambigous pattern is the following:
```
$( $i:iden )* $j:iden
```
as it can never reach the final identifiers


Pattern meta-functions are syntactic sugar for a meta-function taking in tokens and applying the `#pattern_parse` meta-function to them.

Meaning that
```
meta fn foo$( $iden:iden ) {
    ...
}
```
can be thought of as
```
meta fn foo(tokens: []meta.Token) {
    #meta.pattern_parse(tokens, $iden:iden);
    ...
}
```

> _Note_: This is syntactic sugar for a meta-function

### 13.3.1. Parsed pattern meta-functions
```
<meta-pattern-parse-signature> := '$' '(' <meta-pattern> ')'
```
A parsed pattern meta-function will automatically parse its input according to a given pattern.

This meta-function can be thought of as syntactic sugar for
```
meta fn name(tokens: meta.FragmentStream) {
    #pattern_parse(tokens, <pattern>)
}
```
Where `<pattern>` represents the pattern defined in the signature

### 13.3.1. Matched pattern meta-functions
```
<meta-pattern-match-body> := '$' '{' { <meta-match-arm> }+ '}'
<meta-match-arm> := '(' <meta-pattern> ')' '=>' <block-expr>
```
A matched pattern meta functions allows a meta-function to change its behavior based on the data passed into it.

The meta-funciton is essentially syntactic sugar for
```
meta fn name(tokens: meta.FragmentStream) {
    #pattern_match { tokens,
        <pattern-0> => { ... },
        <pattern-...> => { ... },
        <pattern-n> => { ... },
    }
}
```
Where `<pattern-...>` represent the patterns defined in the meta body.

## 13.4. Meta attributes [↵](#13-metaprogramming-)
```
<meta-attribute>          := 'attr' [ '(' <meta-attribute-role> ')' ] 'fn' <name> '(' <meta-attr-signature> ')'
<meta-attribute-role>     := 'derive' '(' <path> { ',' <name> }* ')'
<meta-attr-signature>     := <name> : ? AST type ? [ ',' <name> ':' ? Attribute argument map type ? ] [ ',' ]
                           | <meta-pattern-signature> [ ',' <name> ':' ? Attribute argument map type ? ] [ ',' ]
```

A meta-attribute is a variation on a meta-function and represents an attribute that is applied to a piece of code.

Unlike regular meta-functions, meta-attributes have a strict input signature and return type.
Meta-attributes must take either one of the following as input:
- `meta.Fragment(..)`: This takes in a specific fragment and restrict what the attribute can be applied on
- `meta.Ast`: This takes in any AST sub-tree this attribute is applied on
In additon, a second argument can be passed of type: `meta.AttrArgs`, this contains a collection of key-value pairs of arguments passed to the attribute.

The role of meta-attribute is supplied directly after the `attr` keyword, and is one of the following:
- `full`: consumes the full fragment to which it is attached, allowing the user to use it in any way they like. If the fragment is not returned, the original fragment will not be compiled.\
  This is the default mode of a meta-attribute.
- `peer`: adds code inside of the same scope in which the fragment it is applied to is located. The original fragment is unaffected.
- `member`: adds code within the fragment. The fragment must be an item. The original fragment is unaffected.
- `member_attr`: adds attributes to the any of the members of the item it is applied to. The original fragment is unaffected.
- `accessor`: adds accessors to a field or property. When implemented on a field, it might automatically convert the field into a property, depending on the additional info that is provided.
- `derive(Trait)`: used to add an implementation of the associated trait to the type. This function can be called by either using the meta-attribute directly, or when the trait is given to the [`derive` attribute](#derive-). The original fragment is unaffected.\
  In addition, a derive attribute may also declare a set of helper macros it can use.
  These are defined after the trait within the `derive` role.
  They may take in any amount of additional info.

> _Note_: When a meta-attribute mentions that the original fragment is unaffected, it means that all the original content of the fragment will be retained, it however does get additional code added to it.

Multiple meta-attributes are allowed to share a name, if they have distinct roles.
For example, 2 functions with the same name, but one with a `member_attr` role and the other with a `accessor` role, could exist.
This allows the same name to be used in different contexts, where the different role can be distinguished by its location.

In cases where multiple attributes with the same name would operate on a fragment, both will be ran.

> _Note_: `full` cannot be assigned together with any other role


Some meta-attribute roles require some additional info to be provided:
- `names`: provided the names of the generated fragments. Required for `peer`, `member`, and `accessor`.\
  The value provided to this has to be one of the following, and also defines the name of the generated fragment:
  - `named(...)`: the name will be specific name given to this value
  - `overloaded`: the name will be the same as the fragment its applied on
  - `prefixed(...)`: the name will be that of the fragment, but with the given value as a prefix
  - `suffixed(...)`: the name will be that of the fragment, but with the given value as a suffix
  - `arbitrary`: the name cannot be determined beforehand
- `field_convert`: notifies whether a meta-function might convert a field into a property. Only allowed for `accessor`.

The return type of these attributes depend on the type of meta-attribute being defined:
- `member_attr`: returns an iterator of member names and attribute fragments to be added, i.e. `(String, meta.Fragment(meta.ast.Attribute))`
- `accessor`: returns an iterator of property accessors to be added, i.e. `meta.Fragment(meta.PropertyAccessor)`
- `derive`: return an impl field fragments, i.e. `meta.Fragment(meta.ast.ImplField)`
- otherwise returns an AST sub-tree.

> _Note to LSP developers_: It could be useful to provide the programmer a way of know if a mete-attribute is of kind `full` or `accessor` and let them know code might be changed.

There are also some limitation to where a meta-attribute can be applied:
- `member` and `member_attr`: Only allowed on fragments that may contain members.
- `accessor`: Only allowed of fields and properties.
- `derive(...)`: Only allowed on items

## 13.5. Meta returns

Meta functions may do not have a specific return value, and may therefore return values, types, tokens, ASTs, etc.

Returning a value can be useful when wanting to take in one representation, and outputting a value that has a different representation of it, without requiring a ton of function calls.
Especially when you want to use functionality which is normally private to the library containing the type of the value being returned.

For example, writing json directly in code and returning a json type:
```
let json_obj = #json {
    "foo" = "5",
    "bar" = {
        "baz" = "true",
        "quux" = [ "a", "b", "c" ]
    }
}
```
This can be used to return a `JsonObject` type, and allows private items of the type to be used, as the value is returned, not the code producing the value.

> _Note_: This may also return a value of type `type`

### 13.5.1. Special meta return types

In addition to returning values of a given type, there are special return types with additional meanings, these are the following:
- `meta.TokenStream`: returns a sequence of tokens, which will than be parsed by the compiler within the surrounding code.
- `meta.Fragment(...)`: returns a specific fragment type.
- `meta.Ast`: returns an AST sub-tree.
- `(T, meta.HygieneInfo)`, where `T` is any of the above types: return one of the above types, but additionally provided data with hygiene rules to prevent things like shadowing of variables.

## 13.6. Meta-aliases
```
<meta-alias> := ( 'meta' | 'attr' [ '(' <meta-attribute-role> ')' ]) 'fn' <name> [ <deduced-parameters> ] <meta-signature> '=' <macro-invocation> ';'
```

Meta aliases allow simple way of re-using macros with different inputs, while still achieving the same output.
They can alias both regular meta-functions and meta-attributes.

## 13.6. Meta-function execution

Meta-functions are executed from top to bottom, and outer to inner, on the fragments they are attached to.
If 2 meta-functions are in independent context, there is not guarantee to which will be implemented first.

With the exception of a `full` meta-attribute, the generated code will be added at the end of the current code.
While for non-member fragments, this has no impact, this is important to know when

Therefore, the user can generally **not** rely on having info of any code that is generated by another meta-function.
However, meta-attribute can be forced to wait for another meta-attribute to have been evaluated before or after another meta-attribute.
This is done by wrapping the attribute inside of a `@meta_order` attribute

Whenever meta-functions are executed, they are ran in a sandboxed way, they can therefore not:
- access files on the system
- accces any network
- share state between runs
- have any other side-effects

## 13.6. Meta invocations [↵](#13-metaprogramming-)
```

<meta-invoke> := '#' <simple-path> [ <meta-invocation> ]
<meta-args>   := '(' <tokens> ')'
               | '{' <tokens> '}'
               | '[' <tokens> ']'
```

A meta invocation is used to invoke a meta variable or function.

A meta variable is invoked by just calling the meta variable without any arguments.

Each invocation will be replaced by the code produced by the meta-function.

A meta invocation can be located anywhere in code, but requires the surrounding delimiters to match, as a meta-function may not introduce unmatched delimiters.

A meta-invocation may appear in the following situations:
- [expressions](#9-expressions-)
- [statements](#8-statements-)
- [patterns](#10-patterns-)
- [types](#111-types-)
- [items](#7-items-)

## 13.7. Meta hygiene

The hygiene of a macro depends on what it returns.

If a macro returns a value, it is _hygienic_, as it does not impact the surrounding code, as not variables are declared that are visible outside of the macro invocation.

If a macro return any of the special types, without any `HygieneInfo`, then they are,  _unhygienic_, meaning that they may introduce new variables of items within code.
These variables can shadow pre-declared variables, whenever this happens, a `meta_shadow` lint will be produced.
By default, this lint is set as `@!warn(meta_shadow)`, and can be disabled using the `@allow(meta_shadow)` attribute.

In addition to any of hte special types, if `Hygiene` info is provided, the meta-functin is _partially hygienic_`.
Only the data specified in the hygiene info will be visible outside of the macro invocation, which may still cause some values to be shadowed.
In this case, the same `meta_shadow` lint will be used.

To ensure that variable names generated within not to conflict with any name that could be passed into the generated code, the `make_unique_name()` function can be used.
This is accessible from the context provided into the meta function.

## 13.8. Meta utilities [↵](#13-metaprogramming-)

To help with the creation of meta-functions, the `meta` library includes additional meta-functions to help writing of them.

### 13.8.1. `#tokenize`, `partial_synth` & `#synthesize` [↵](#138-meta-utilities-)

These 3 meta-functions are all used to generate a useable representation from raw tokens and values provided to them.
While they all take the same data, their outputs are slightly different
- `#tokenize` will convert the data into a token stream
- `#partial_synth` will convert the data into a fragment stream, where directly written symbols will be passed as tokens, and included values will use their ast fragment representation
- `#synthesize` will convert the data into an AST subtree.

In addition to just converting the provided code to tokens, it also allows the capturing of values and directly adding them into the code.

This can be done in a similar way to how pattern meta values are defined, i.e. `$name`, where `name` is a variable declared within the same scope.
It will automatically interpret the provided value and convert it into the corresponding code.
To use an expressions directly within these functions, the expression can be wrapped in `$(...)`, where the result of the expression will be used.
An expression can also be inserted as a literal value, this is done by prefixing the expressions with `literal:`, i.e. `$(literal: ...)`.

In addition, any type that can be dereferenced into a slice or an optional type may be inserted as their individual elements.
This can be done by surrounding the code that needs to be expended using `${}`, and either `*` for slices and `?` for optional types.

These meta-function can also output additional metadata, telling the compiler which variable created by the meta-function should be visible in the scope they are used in.T
This can be done by adding `output <names> =>` before the code that needs to be generated, where `<names>` is replaced with a comma separated list of the names to 'export' from the meta-function's code.

For example
```
// Convert to a list of 4 tokens: 'foo', '(', ')', the tokens that represent `bar` , and ';'
#tokenize {
    foo($bar);
}

// Converts to a list of 2 tokens: 'foo' and '(', then followed by an ast fragments, and then followed by the 2 tokens ')' and ';'
#partial_synth {
    foo($bar);
}

// Convert to an AST function call node
#synthesize {
    foo($bar);
}
```

> _Note_: the output of `#partial` can not be directly returned from the function, but can be used to pass onto other meta-functions that take in meta-patterns

#### Meta blocks [↵](#1361-tokenize--synthesize)

In addition, the above 2 meta-function also support some syntactic sugar to make writing them simpler.
Either can be replaced with a so-called metablock, which convert a given piece to code to either a set of tokens or a parse AST subtree, depending on the type expected.

This means that `#tokenize { ... }` or `#synthesize { ... }`, can simple be written as `#{ ... }`.

### 13.8.2. `#pattern_parse` [↵](#138-meta-utilities-)

The `#pattern_parse` meta-function allows for a slice of tokens to be parse according to a provided pattern.

The possible patterns are defined in the following [section](#133-pattern-meta-functions-).

For example:
```
fn meta(tokens: []meta.Token) -> ... {
    #meta.pattern_parse { tokens => $name:name };
    // `name` is available here and has the type `meta.ast.Name`
}
```

### 13.8.3. `#pattern_match` [↵](#138-meta-utilities-)

The `#pattern_match` meta-function is similar to `#pattern_parse`, but allows multiple meta patterns to be provided and will choose the first matching one.

The match will use the first pattern that matches the tokens that are passed to the meta-function.
If no matching pattern is found, and no wild-card is provided, it will generate a compiler error and return from the meta function.

A pattern within the pattern match must be surrounded by parentheses.
In addition, a `_` can be use as a wildcard pattern.

For example:
```
fn meta(tokens: []meta.Token) -> ... {
    #meta.pattern_match {
        tokens,
        ($name:name) => {

        },
        _ => {

        }
    }
}
```

### 13.8.4. `#invoke_repr` [↵](#138-meta-utilities-)

The `#invoke_repr` meta-function allows the program to get the token or AST representation of the value passed to meta-function invocation.
This allows the meta-function to use the representation, in addition to the computed value, of an argument.

`#invoke_repr` can only be called on the parameters of the meta-function.

For example, when we invoke the following macro:
```
#stringify(1 + 1)
```
the `#invoke_repr` allows use to get the actual expression passed:
```
meta fn stringify(val: i32) -> (i32, &str) {
    // Get the representation that was passed to the invocation
    val_repr := #invoke_repr(val);
    (val, val_repr.to_str())
}
```
This would return us the value `(2, "1 + 1")`

### 13.8.5. `#library` & `#package` [↵](#138-meta-utilities-)

The `#library` and `#package` meta-functions are special macros that can be used to contruct path refering to the library and package in which the meta-function is defined respectively.

# 14. Operators [↵](#tables-of-contents)

An operator is a set of non-alphanumeric symbols (with some exceptions) that can represent an operation to 1 or 2 expressions.
They are generally split into 3 categories:
- prefix unary operators, these come before a sub-expression
- postfix unary operators, these come after a sub-expression
- infix binary operators, these come betweeen 2 sub-expressions

Operators must be separated by non-operator symbols, otherwise they will be interpolated as 1 single operator, meaning that multiple prefix expression, operators must be separated by parentheses.
In the future, an additional way of separating these might be decided.

Operators, unlike other syntax, is dependent on whitespace, as it is used to consistently differentiate between prefix, infix, or postfix.
These rules are: 
- unary operator need to be directly attached to the value they operator on, i.e. no whitespace may be between them
- whitespace around binary operators need to be consistent, there is either no whitespace between it and the values, or there is whitespace on either end.

> _Todo_: Specify which operators are include in `minoa:core.minimal`

## 14.1. Operator items [↵](#14-operators-)

```
<op-set>      := <base-op-set> | <ext-op-set>
<base-op-set> := { <attribute> }* 'op' 'trait' <name> [ '|' <name> ] '{' <op-elems> '}'
<ext-op-set>  := { <attribute> }* 'op' 'trait' <name> ':' <simple-path> { '&' <simple-path> }* '{' <op-elems> '}'

<op-elems>    := <op-elem> { ',' <op-elem> } [',']
<op-elem>     := <op-decl> | <op-contract>
<op-decl>     := [ <op-mod> ] <op-kind> 'op' <operator> ':' <name> [ '=' <expr> ]
<op-mod>      := 'lazy' [ 'chain' | 'consume' ]
               | 'chain'
               | 'consume'
<op-kind>     := 'prefix' | 'postfix' | 'infix' | 'assign'

<op-contract> := 'invar' <block-expr>
```

Operator sets are used to declare new operators and their related properties.
The majority of core operators are also implemented using this system.

Operator sets also create the associated trait and underlying methods to use these within code.
All operator sets and associated operators are public symbols.

An operator set is allowed to define one of the following:
- A precedence wich will be used by all infix operators within the set, or
- Other operators set that this set extends, and therefore requires to have implemented to implement the 'derived' operator set.

Each operator within the set must have the following properties:
- An operator kind
- The operator's punctuation in code
- The name of the corresponding method (needs to be unique for each operator)

And optionally contains:
- return type
- default implementation

Operators are defined within this set, starting with the kind of operator, these are:
- Prefix operators that apply to the expression on the right of them
- Postfix operators that apply to the expression on the left of them
- Infix operators that apply to the expressions on either side of the operator
- Assign operators that modify the expression on left of them using the expression on the right

Precedence is only applied on the infix operators. Prefix, postfix, and assign expressions have hardcoded precedences, and can therefore not be defined explicitly.

If no return type is provided, the operator will return the operator trait's associate `Output` type alias. Assign operator cannot have a return type.

When any infix operator is defined, but no precendence of set to extend is provided, the expression containing the infix operator and its operands will be required to be surrounded by parentheses, if these are other operator expressions.

> _Note_: While extending another set allows new operators to be added, any operator contained within the set being extended cannot be overriden

Operator sets can also define a set of invariant contracts, to which all contained operators must adhere to, for example: commutativity.

TODO: disallow identical looking sets of characters
Operator punctuation may contains a range of different characters, which can be found [here](allowed-symbols.md).

### 14.1.1. Operator modifiers [↵](#141-operator-items-)

Each operator may have some additional modifiers applied on them to define how they will handle their operands

#### `lazy` [↵](#1411-operator-modifiers-)

This indicates that that the right-hand operand will be passed to the operator as a [lazily parameter](#lazy-parameters).

This is only allowed on infix and assign operators.

#### `chain` [↵](#1411-operator-modifiers-)

A chaining expression indicates that the left-hand operand will be passed on to callee.

How the chaining is handled, depends on the right-hand operand, which is one of the following:
- function: The left-hand operand will be passed as the first argument of the callee, this requires that:
  - the function has at least 1 argument
  - its first argument is label-less
  - only has the arguments after the first argument passed
- method: The left-hand operand will used as its receiver
- closure: the closure has exactly 1 argument, either explicitly defined, or it requires for `$0` to be used within the closure
- autoclosure: the closure is created if the implicit `$0` variable is used
  
Otherwise this operator is called just like any other infix operator.

This is only allowed on infix operators.

#### `consume` [↵](#1411-operator-modifiers-)

This indicates that the operator will receiver a right-hand operand, consisting of a list of comma-separated expression.
These values will be consumed until an expression on its right side is encountered with a lower precedence.

The values may be provided either as:
- a [parameter pack](#123-parameter-packs-)
- a tuple, if not all elements are provided, but the elements they represent are optional, these values will default to `.None`
- an iterable type that can generate a number of values

In addition, a `consume` operator may also be directly followed by a loop expression that return a value at the end of each iteration, or using a `break` expression.
In this case, the loop will automatically be wrapped using an iterable closure.

While a comma normally has the lowest precedence, for consuming operators, this is not taken into account when selecting a cut-off point.

Unlike non-`consume` operators, this returns the left-hand size

This is not allowed for postfix operators

#### `by_ref` [↵](#1411-operator-modifiers-)

This indicates that the operator will always take a reference to both values, as it does not need them to be moved into the operator.

An example of this are the comparison operators.

### 14.1.2. Implementing operators on types [↵](#141-operator-items-)

Implementing operators on a type is done by implementing it's trait, like usual.

It the operator includes an infix or assign type, they both take the same `Rhs` argument within the trait, so must be specified for anything other than the type being implemented.

In case a return type is not defined on at least 1 operator (except for assign operators), the Output type for the operator needs to be defined as `type Output = ...;`

Below is a table of operators and their respective method signature

 Op Type | modifier  | Signature
---------|-----------|---------------------------------------------
prefix   | n/a       | `fn name(self) -> Self::Output`
prefix   | `consume` | `fn name(self) -> Rhs`
postfix  | n/a       | `fn name(self) -> Self::Output`
infix    | n/a       | `fn name(self, other: Rhs) -> Self::Output`
infix    | `chain`   | `fn name(self, other: Rhs) -> Rhs`
infix    | `consume` | `fn name(self, other: Rhs) -> Self`
assign   | n/a       | `fn name(&mut self, other: Rhs)`

### 14.1.3. Allowed characters. [↵](#141-operator-items-)

This defines a list of characters that are allowed to be used in user defined operators.

The characters generally include mathematical opreators, miscellaneous characters, dingbats unicode blocks, ect.

More information and a description about these characters can be found [here](operator-symbols.md)

> _Todo_: Fix link to character descriptions

```
<custom-operator>   := <operator-head> { <operator-continue> }*
                     | '.' { <operator-continue> | '.' }*
<operator-head>     := '!'
                     | '%'
                     | '&'
                     | '*'
                     | '+'
                     | '-'
                     | '/'
                     | '<'
                     | '='
                     | '>'
                     | '?'
                     | '|'
                     | '~'
                     | ? U+00A1 - U+00A7 ?
                     | ? U+00A9 ?
                     | ? U+00AB ?
                     | ? U+00AC ?
                     | ? U+00AE ?
                     | ? U+00B0 ?
                     | ? U+00B1 ?
                     | ? U+00B6 ?
                     | ? U+00BB ?
                     | ? U+00BF ?
                     | ? U+00D7 ?
                     | ? U+00F7 ?
                     | ? U+2016 ?
                     | ? U+2017 ?
                     | ? U+2020 - U+2027 ?
                     | ? U+2030 - U+203E ?
                     | ? U+2041 - U+2053 ?
                     | ? U+2055 - U+205E ?
                     | ? U+2190 - U+23FF ?
                     | ? U+2500 - U+2775 ?
                     | ? U+2794 - U+2Bff ?
                     | ? U+2E00 - U+2E7F ?
                     | ? U+3001 - U+3003 ?
                     | ? U+3008 - U+3020 ?
                     | ? U+3030 ?
<operator-continue> := <operator-head>
                     | ? U+0300 - U+036F ?
                     | ? U+1DC0 - U+1DDF ?
                     | ? U+20D0 - U+20FF ?
                     | ? U+FE00 - U+FE0F ?
                     | ? U+FE20 - U+FE2F ?
                     | ? U+E0100 - U+E01EF ?
```

## 14.2. Special operators [↵](#14-operators-)

Special operators are a set of operator that requires

Some operators have some specific syntax to them that cannot be directly implemented and need compiler support


### 14.2.1. Borrow operators [↵](#142-special-operators-)
```
<borrow-op> := '&' [ 'raw' ] [ 'mut' ]
```

Borrow operators are prefix operators.
When applied to a place expression, the operator takes a reference (of pointer) to the location the value refers to.
The memory location is then put in a borrowed state for the duration of the reference, meaning until the last use of the borrowed value.

If a shared borrow (`&`) is taken, the value within the location cannot be mutated, but can be shared and read.
Otherwise when a mutable borrow (`&mut`) is taken, the value within the location can be mutated and read, but cannot be shared.

When the operator is applied to a value expression, a temporary value will be created.

The associated for traits for these operators are
- `&`: `Borrow`
- `&mut`: `BorrowMut`

This operator has the `BorrowDeref` precedence.

#### Raw borrow operators [↵](#1421-borrow-operators-)

The raw borrow operators are special cases of the borrow operator, which instead of borrowing the values, get the memory address of the value.
This is indicated using the `raw` keyword.

`&raw` creates a `^T`, and `&raw mut` creates a `^mut T`.

The raw borrow operators should be used in a place where the borrow operator could evaluate to a place that is not properly aligned, or does not store a valid value as determined by its type, or whenever creating a reference that would introduce inccorrect aliassing assumptions.
These are situations which would result in [illegal behavior](#23-illegal-behavior-).

Unlike other operators, the raw borrow operator cannot be implemented.

### 14.2.2. Derefence operator [↵](#142-special-operators-)
```
<deref-op> := '^'
```

The derefence operator is a postfix operator.
When applied to a pointer, in denotes a pointed-to locaton.
If the expresson is of type `&mut T` or `^mut T`, and is either a local variable or (possibly nested) field variable, or is a mutable-place operator, then the resulting memory location can be assigned to.

Dereferencing a raw pointer requires an `unsafe` context.

A special variant of the deref operator is `DerefMove`, allowing either the derefernced value or one of its fields to be moved out of its location.
A useful example of this would be for types such as smart-pointers.

The exact variant of the derefernce will be decided based on the surrounding context

The associated traits for this operator are:
- immutable: `Deref`
- mutable: `DerefMut`
- move: `DerefMove`

This operator has the `BorrowDeref` precedence.

### 14.2.3. Try operator [↵](#142-special-operators-)
```
<try-op> := '?' | '!'
```

Try operators are postfix operators.

Try operator are used to affect the control flow of a function when an erronous value is produced.
If a valid value will be generated, it will return this value.

#### Propagating try [↵](#1423-try-operator-)

The propagating try operator (`?`) allows a function to shortcut an return an erronous value from the current function.
It will cause also all in-scope [defer-on-error statements](#841-defer-on-error-statement-) to be evaluated.

The associated trait for the operator is `Try`

> _Note_: This should not be confused with the ['err'-checked field access](#916-field-access-)

The operator has the `Unary` precedence.

#### Unwrapping try [↵](#1423-try-operator-)

The unwrapping try operator (`!`) will cause a program to panic if an erronous value is encountered.

The associated trait for the operator is `TryUnwrap`

The operator has the `Unary` precedence.

## 14.2.4. Contract capture operator [↵](#142-special-operators-)
```
<contract-capture-op> := '$'
```

Contract capture operators are postfix expressions.

Contract operators are only allowed inside of `post` contracts.
They allow the value of an expression to be captures at the start of a function, to use it in the post contract at the end of the function.
The value captures must be a a `Copy` type.

This operator cannot be manually implemented, as `$` has multiple special meanings.

The operator has the `Unary` precedence.

## 14.2.5. Logical boolean operators [↵](#142-special-operators-)
```
<lazy-bool-op> := '&&' | '||'
```

Lazy boolean operators are infix operators.
Lazy boolean operators can only be applied to boolean values and cannot be overloaded.
`||` represents a logical or, and `&&` represents a logical and, they differ from tier single character counterparts `|` and `&`, in that the right hand operand is only evaluated if the left-hand operand does not already determine the result of the expression.

That is, `||` only evaluates the right-hand operand if the left-hand operand evaluates to `false`.
On the other hand, the `&&` only evaluated the right-hand operand if the left-hand operand evaluates to `true`.

They are [lazy infix operators](#lazy)

The `&&` operator has the `LogicalAnd` precedence, and `||` has the `LogicalOr` precedence.

## 14.2.6. catch operator [↵](#142-special-operators-)
```
<catch-op> := '??'
```

Catch operators are infix operators.

This is similar to the or-else operator, but instead of being based on a 'thruthy' value, it is based on an explicit erronous value.

This is shorthand for the non-error capturing [catch expression](#929-catch-expression-).
Unlike the catch expression, this operator follows a different precedence.

The associated trait is `Catch`.

The operator has the `Select` precedence.

## 14.2.7. In-place operator
```
<inplace-operator> := '<-'
```

The in-place operator allows for the direct construction of a type directly in its memory location.
This can be useful in case the type takes up a lot of space and allows to avoid generating an intermediate.

The in-place operator may take either of the following expressions on it's right-hand side:
- a [constructing expression](#910-constructing-expression-)
- a function or closure, taking a single argument of type `&mut MaybeUninit(T)`
- an [initializer](#716-initializers-)

This operator has no associated trait (i.e. can't be manually implemented), and has assign precedence.

## 14.3. Core operators [↵](#14-operators-)

Core operators have no special meaning, but are defined within the core library and have a use for builtin types

## 14.3.1. Comparison operators [↵](#143-core-operators-)

```
<comparison-op> := <eq-op> | <iden-op> | <ord-op> | <total-ord-op>
<eq-op>        := '==' | '!='
<iden-op>      := '===' | '!=='
<ord-op>       := '<' | '<=' | '>' | '>=' | '<=>?'
<total-ord-op> := '<=>'
```

Comparison operators are infix operators.
Parentheses are required to chain comparisons, e.g. `a == b == c` is invalid, but `(a == b) == c` is valid (when the type are compatible).

Unlike most infix operators, the traits to overload these operators are used more generally to indicate when a type may be compared and will likely be assumed to define actual comparisons by functions that use these are trait bounds.
Code can then use these assupmptions when using the operators.

Comparison operator are [`by_ref`](#by-ref)
Unlike most binary operators, these operators implicitly take a shared borrow of their operands, evaluating them as place expressions.

The following operators and their respective trait functions are

Operator | Meaning                          | Trait method
---------|----------------------------------|----------------------
`==`     | Equal                            | `Equality::eq`
`!=`†    | Not equal                        | `Equality::ne`
`===`    | Identical                        | `Identity::identical`
`!==`‡   | Not Identical                    | N/A
`<`*     | Less than                        | `Ord::lt`
`<=`*    | Less than or equal to            | `Ord::le`
`>`*     | Greater than                     | `Ord::gt`
`>=`*    | Greater than or equal to         | `Ord::ge`
`<?`*    | Partial less than                | `Ord::partial_lt`
`<=?`*   | Partial less than or equal to    | `Ord::partial_le`
`>?`*    | Partial greater than             | `Ord::partial_gt`
`>=?`*   | Partial greater than or equal to | `Ord::partial_ge`
`<=>?`   | Partial order comparison         | `Ord::partial_cmp`
`<`*     | Total less than                  | `TotalOrd::lt`
`<=`*    | Total less than or equal to      | `TotalOrd::le`
`>`*     | Total greater than               | `TotalOrd::gt`
`>=`*    | Total greater than or equal to   | `TotalOrd::ge`
`<=>`    | Total order comparison           | `TotalOrd::cmp` 

† By default implemented in terms of `!(a == b)`

‡ Always implemented as `!(a === b)`

\* By default implemented based on `partial_cmp`

\** By default implemented based on `cmp`

The operators have the `Compare` precedence.

Below is an explenation of these traits:

> _Note_: This might be replaces with a simpler explanation, and have its definition moved into the `core` and `std` library

#### `Equality`

`Equality` is a trait that defines what types can be compared to each other, i.e. which are equal to each other.

This trait defines the `==` and `!=` operators.

Values implementing this can be compared to each other.

Equality does not neccesarily imply that a value is equal to itself, for example `f32::NAN != f32::NAN`

Equality has the following properties:
- reflexitivy, i.e. `x == x` is always true
- symmetry or communitativity, i.e. `a == b` implies `b == a`
- transitive, i.e. `a == b` and `b == c` implies `a == c`

There is no guarantee that a value is equal to itself.

When 2 values are compared, they might still have distinct bitpattern underlying the 

#### `Identity`

`Identity` is a trait is similar to `Equality`, but with some additional guarantees:

This trait defines the `===` and `!==` operators.

values implementing this can be used to check if 2 values are identical.

While identity technically has the same properties to equality, it has 1 major distinction that makes this point a bit moot.
Since identity is used to check if 2 values are exactly equal, there can only 1 value that is equal to a value, i.e. the value itself.

> _Note_: The identity operator does **not** imply that 2 values have the same memory location

> _Note_: While most implementation are a memcmp, some types, such as smart pointers, might change this definition to "pointing to the same address"

> _Note_: If `Identity` is implemented and `Equality` is not implemented (either derived or explicitly implemented), an implementation based on `Identity` will be added

#### `Ord`

`Ord` is a trait that defines what types can be ordered relative to each other, to be specific, a partial order.

This trait defines the following operators, which are split up in 3 categories:
- `<`, `<=`, `>` `>=`: comparison operators
- `<?`, `<=?`, `>?` `>=?`: partial-comparisom operator, similar to comparison operators, but may also indicate an 'undefined' comparison
- `<=>?`: Partial ordering, the main operator, of which the other operators, by default, derive their implementation from

`Ord` has  `Equality` as a super-trait.

Values implementing this be compared to each other and have an order between them.

Ordering has the following properties:
- communitativity, i.e. `a < b` implies `a > b`
- transitive, i.e. `a < b` and `b < c` implies `a < c`

#### `TotalOrd`

`TotalOrd` is a trait that defines what types can be ordered relative to each other using total ordering.

`TotalOrd` has `Equality` as a super-trait.

Total ordering defined the following operators:
- `<!`, `<=!`, `>!`, `>=!`: total-comparison operator
- `<=>`: total-ordering operator, the main operator, of which the other operators, by default, derive their implementation from

An example of how total ordering and partial ordering differ, `-0.0 == 0.0` in partial ordering, but `-0.0 < 0.0` in total ordering.

> _Todo_: Add 'identical' operators, i.e. === and !==, useful for thing like references without implicitly dereferning the parameter before hand

> _Note_: If `TotalOrd` is implemented and `Ord` is not implemented (either derived or explicitly implemented), an implementation based on `TotalOrd` will be added

## 14.3.3. Range operators

```
<range-op> := '..' | '..='
```

Range operators can be prefix, infix, or postfix.

The range operators, like their name implies are used to generate a range between 2 values.

The following operators, their respecitive trait methods, resultant types, and ranges are

Operator     | Syntax        | Trait Method                     | Type               | Range
-------------|---------------|----------------------------------|--------------------|--------------------
Infix `..`   | `start..end`  | `Range::range`                   | `Range`            | start <= x < end
Postfix `..` | `start..`     | `RangeFrom::range_from`          | `RangeFrom`        | start <= x
Prefix `..`  | `..end`       | `RangeTo::range_to`              | `RangeTo`          | x <= end
Infix `..=`  | `start..=end` | `RangeInclusive::range_inc`      | `RangeInclusive`   | start <= x <= end
Prefix `..=` | `..=end`      | `RangeToInclusive::range_to_inc` | `RangeToInclusive` | x <= end

## 14.3.4. Contains operator [↵](#14-operators-)

```
<contains-op> := 'in' | '!in'
```

Contains operators are infix operators.p
A contains operator can be used to check if a value is contained within another value, e.g. if a value is contained by a range or collection.
There is both a positive and negated version.

This operator differs from other operators, by the fact that it can be a combination of a non-alphanumeric and alphanumeric characters.

The following operators and their respective trait functons are:

Operator | Meaning          | Trait method
---------|------------------|--------------------------
`in`     | Contains         | `Contains::contains`
`!in`†   | Does not contain | `Contains::not_contains`

† By default implemented in terms of `!(a in b)`

The operator has the `Contains` precedence.

## 14.2.5. Or-else operator [↵](#14-operators-)

```
<or-else-op> := '?:'
```

Or-else operators an infix operators.

The or-else works based on the value of the left-hand operand.
If the left-hand operand evaluates to a 'thruthy' value, the left hand operand is returned.
Otherwise if it evaluates to a non-'truthy' value, the right operand is evaluated.

'Truthy' can imply more than explicitly `false` or 'none' operations, i.e. `0` is not a 'thruthy' value. 

The associated trait is `OrElse`

The operator has the `Select` precedence.

## 14.3.6. Pipe operators [↵](#14-operators-)

```
<pipe-op> := '|>' | '<|'
```

Pipe operators are infix operators.

Pipe operators are used to pipe a value into another expresion, this can be done in 2 ways:
- chaining: where the left-hand operator is moved int the right-hand operand
- comsuming: the results of the right-hand operand is moved into the left-hand operand

When it comes to the operands of the chaining an consuming operator, they follow their respective `chain` and `consume` operator modifier's behaviors.

The associated traits for this operators are:
- `|>`: `PipeChain`
- `<|`: `PipeConsume`

The operator has the `Pipe` precedence.

## 14.3.7. Other operators [↵](#14-operators-)

The following section contains a list of other prefix, postfix and infix operators that weren't mentioned in their own individual sections

Prefix operators:

Operator | type                  | Trait | precedence | meaning                                        | Example
---------|-----------------------|-------|------------|------------------------------------------------|----------------------------------------
`+`      | numeric               | `Pos` | `Unary`    | unit operators, return the same value as given | `+a == a`
`-`      | signed/floating point | `Neg` | `Unary`    | negation                                       | `-a != -1 if a == 1` and `-(-a) == a`
`-%`     | signed/floating point | `Neg` | `Unary`    | wrapping negation                              | `-%128 == -128`
`!`      | bool                  | `Not` | `Unary`    | Logical not                                    | `!false == true`
`!`      | integer               | `Not` | `Unary`    | Bitwise not                                    | `!0 == usize::MAX` 


Infix/binary operators:

Operator | type                  | Trait          | precedence  | meaning                                                         | Example
---------|-----------------------|----------------|-------------|-----------------------------------------------------------------|----------------------------------------
`+`      | numeric               | `Add`          | `AddSub`    | Addition, panics on overflow (in debug)                         | `1 + 2 == 3`
`+%`     | integer               | `WrappedAdd`   | `AddSub`    | Addition, wraps on overflow                                     | `u32.MAX +% 1 == 0`
`+\|`    | integer               | `SaturateAdd`  | `AddSub`    | Addition, saturates on overflow                                 | `u32.MAX +\| 1 == u8.MAX`
`+?`     | integer               | `TryAdd`       | `AddSub`    | Addition, returns Some, or None on overflow                     | `1 +? 2 == Some(3)` or `u32.MAX +? 1 == None`
`-`      | numeric               | `Sub`          | `AddSub`    | Subtraction, panics on underflow (in debug)                     | `3 - 2 == 1`
`-%`     | integer               | `WrappedSub`   | `AddSub`    | Subtraction, wraps on underflow                                 | `0 -% 1 == u32.MAX`
`-\|`    | integer               | `SaturateSub`  | `AddSub`    | Subtraction, saturates on underflow                             | `0 -\| 1 == 0`
`-?`     | integer               | `TrySub`       | `AddSub`    | Subtraction, returns Some, or None on overflow                  | `1 -? 2 == Some(3)` or `0:u32 -? 1 == None`
`*`      | integer               | `Mul`          | `MulDivRem` | Multiplication, panics on overflow (in debug)                   | `2 * 3 == 6`
`*`      | floating point        | `Mul`          | `MulDivRem` | Multiplication, according IEEE-754-2008                         | `1.5 * 2.0 == 3.0`
`*%`     | integer               | `WrappedMul`   | `MulDivRem` | Multiplication, wraps on overflow                               | `128'u8 *% 3 == 128'u8`
`*\|`    | integer               | `SaturateMul`  | `MulDivRem` | Multiplication, saturates on overflow                           | `128'u8 *\| 3 == 255'u8`
`*?`     | integer               | `TryMul`       | `MulDivRem `| Multiplication, returns Some, or None on overflow               | `64'u8 *? 2 == Some(128)` or `128'u8 *? 2 == None`
`/`      | integer               | `Div`          | `MulDivRem` | Division, panics on divide by 0 (traps in non-debug)            | `6 / 2 == 3`
`/`      | floating point        | `Div`          | `MulDivRem` | Division, according IEEE-754-2008                               | `3.0 / 1.5 == 2.0`
`/?`     | integer               | `TryDiv`       | `MulDivRem `| Multiplication, returns Some, or None on divide by 0            | `128'u8 /? 2 == Some(2)` or `128'u8 /? 0 == None`
`%`      | numeric               | `Rem`          | `MulDivRem` | Remainder*, panics on divide by 0 (traps in non-debug)          | `5 % 2 == 2` or `7.0 % 1.5 == 1.0`
`%%`     | numeric               | `RemFloored`   | `MulDivRem` | Floored remainder**, panics on divide by 0 (traps in non-debug) | `5 % 2 == 2` or `7.0 % 1.5 == 1.0`
`\|`     | integer               | `Or`           | `BitOr`     | Bitwise or                                                      | `0x1010  \| 0x1100 == 0x1110`
`!\|`    | integer               | `Nor`          | `BitOr`     | Bitwise not-or                                                  | `0x1010 !\| 0x1100 == 0x0001`
`&`      | integer               | `And`          | `BitAnd`    | Bitwise and                                                     | `0x1010  & 0x1100 == 0x1000`
`!&`     | integer               | `Nand`         | `BitAnd`    | Bitwise not-and                                                 | `0x1010 !& 0x1100 == 0x0111`
`&!`     | integer               | `Mask`         | `BitAnd`    | Bitwise masking (and if inverse of `b`)                         | `0x1010 &! 0x1100 == 0x0010`
`~`      | integer               | `Xor`          | `BitXor`    | Bitwise xor                                                     | `0x1010  ~ 0x1100 == 0x0110`
`!~`     | integer               | `Xnor`         | `BitXor`    | Bitwise not-xor                                                 | `0x1010 !~ 0x1100 == 0x1001`
`<<`     | integer               | `Shl`          | `ShiftRot`  | Bit-shift left                                                  | `0x101 << 3 == 0x101000`
`<<\|`   | integer               | `SaturateShl`  | `ShiftRot`  | Bit-shift left, saturates if 1 bit is shifted out               | `0x1'u8 <<\| 8 == 0xFF`
`>>`     | signed                | `Shr`          | `ShiftRot`  | Bit-shift right (implicitly arithmetic shift)                   | `0x10..01  >> 3 == 0x11110..00`
`>>`     | unsigned              | `Shr`          | `ShiftRot`  | Bit-shift right (implicitly logical shift)                      | `0x10..01  >> 3 == 0x00010..00`
`>>-`    | integer               | `Shra`         | `ShiftRot`  | Explicit arithmetic bit-shift right                             | `0x10..01 >>- 3 == 0x11110..00`
`>>+`    | integer               | `Shrl`         | `ShiftRot`  | Explicit logical bit-shift right                                | `0x10..01 >>+ 3 == 0x00010..00`
`*<<`    | integer               | `Rotl`         | `ShiftRot`  | Bitwise rotate left                                             | `0x1010..1010 *<< 3 == 0x0..1010101`
`>>*`    | integer               | `Rotr`         | `ShiftRot`  | Bitwise rotate right                                            | `0x1010..1010 >>* 3 == 0x0101010..1`
`++`     | str/array             | `Concat`       | `AddSub`    | Concatinate 2 arrays (sizes must be known at compile time)      | `[1, 2, 3, 4] ++ [5, 6, 7, 8] == [1, 2, 3, 4, 5, 6, 7, 8]` or `"hello" ++ " " ++ "world" == "hello world"`
`**`     | numeric               | `Repetition`   | `MulDivRem` | Raise number to the power of `n`                                | `3 ** 4 = 81`
`**`     | str/array <-> integer | `Repetition`   | `MulDivRem` | Repeat an array N times (size must be known at compile time)    | `[1, 2] ** 3 == [1, 2, 1, 2, 1, 2]` or `"ab" ** 3 == "ababab"`

\* Uses truncating division, meaning `remainder = dividend % divisor` will return a value with the same sign as the dividend.

** Uses floored division, maning `remainder = dividend % divisor` will return a value with the same sign as the divisor.

> _Note_ When reading the above table in plain text, any occurance of `\|` is actually just `|`, but requires the `\` as it is otherwise interpreted as a the next value in the table

## 14.4. Assginment operators [↵](#14-operators-)

```
<assign-op> := <basic-assign-op> | <compound-assign-op>
<basic-assign-op> := '='
<compound-assign-op> := <infix-op> '='
```

Assignment operators are infix operators.

Assignment operators moves a value into a specific place, or modifies a value.

The left-hand operand of hte assignment operator must be an assignment expression, in the most simple case, the aasignee is a simple place expression and the below specificiation assumes this ito simplify the explenation.

An assignment expression uses the following terms in its expression:
```
'assignee' = 'assigned value'
```

### 14.4.1. Basic assignment [↵](#144-assginment-operators-)

Evaluating assignment expressions begins by evaluating its operands.
This assigned value will be evaluated first, followed by the assginee expression.

> _Note_: Unlike other expressions, the right-hand operand is evaluated before the left hand expression

Before assignment, the assignment will first drop the current value of hte assigned place, unless the place is an uninitialized value.
Next, it will either copy or move the assigned value in the location of hte assignee.

### 14.4.2. Destructuring assignment [↵](#144-assginment-operators-)

Destructuring assignment is a counterpart to destructuring patterns for variable declarations, permitting assignment of complex values such as tuples and structures.

In contrast to destructuring declarations using `let`, patterns may not appear on the left-hand side of an assignment due to syntactical ambiguities.
Instead a group of expressions are designated to be [assignee expressions](#assign-expressions-), and are permitted on the left-hand side of an assignment.
Assignee expressions are then desugared to pattern matches followed by subsequent assignments.
The desugared patterns must be irrifutable: in particular, this means that only slice pattens whose lenght is known at compile time, and the trivial slice `[..]` are permitted during structuring assignment.

Underscore experssions and empty range expressions may be used to ignore certain values, without binding them.

### 14.4.3. Compound assignment [↵](#144-assginment-operators-)

Compound assignment expressions combine infix operators with assignment expressions.

The operator used for a compound assignment always ends on a '=', which is used to indicate assignment expressions.

Unlike other assginee operands, the assginee operand must be a place experession.

If both types are primitives, the modifying operand will be evaluated first, followed by the assignee.
It will then set the value of the assignee to the value of perfroming the operation of the operator on the values of hte assignee and modifying operand, and then assign it to the assignee.

> _Note_: Unlike other experssions, the right-hand operand is evaluated before the left-hand operand
> With the exception of a lazy operator, as the right-hand operand will just be passed as a closure, meaning it will be executed after the left-hand operand.

Othewise, the expression is syntactic sugar for calling a function of the overloaded compound assignment operator.
A mutable borrow to the assignee is automatically taken.

### 14.4.4. Core assignment Operators [↵](#144-assginment-operators-)

Core assignment operators, like [core operators](#143-core-operators-), are assignment operators that are defined within the core library.

#### Other operators [↵](#1444-core-assignment-operators-)

operator | type    | trait       | meaning                      | example
---------|---------|-------------|------------------------------|---------
`+=`     | numeric | `AddAssign` | Addition, panics on overflow | `a += 2`

## 14.5. Literal operators [↵](#1411-assginment-operators-)

Literal operators are special pseudo-operator that work on literals, and not values.

When calling an operator label on a literal, there may be no space on either side of the `:`.

### 14.5.1. Literal operator item [↵](#145-literal-operators-)
```
<literal-operator-item> := 'literal' 'trait' <name> '(' <type> ')' '->' <type> ';'
```

A literal operator items, as their name implies, declares a new literal operator which can be used to convert a literal to a given value.

These are simpler to declare than an operator set, as they contain fixed functionality and only require the types it operators on to be declared.
It is declared as a special trait using the `literal` weak keyword, which is then proceeded by the name of the trait defining the literal operator.

Between the parentheses, the type of the literal is passed, these are limited to:
- `core:.DecLiteral`
- `core:.DecFloatLiteral`
- `core:.BinLiteral`
- `core:.OctLiteral`
- `core:.HexLiteral`
- `core:.HexFloatLiteral`
- `core:.CharLiteral`
- `core:.StringLiteral`

Corresponding to the type of the available literals.
Finally, the return type of the literal operator is defined.

Using this info, the literal operator will internally create the following trait that is associated with this item:
```
pub trait <name>(<lit-type>) {
    use core:.{Result, CompileError};

    const fn check(lit: <lit-type>) -> Result((), CompileError);
    fn lit_op(lit: <lit-type>) -> <ret-type>;
}
```

Where `<name>`, `<lit-type>`, and `<ret-type>` are replaced by the value they are respective by.

The `check` function is a compile-time function that will run for every use of the literal operator and is required to report any invalid data within the literal.

The `lit_op` function is used to convert the literal type to the return type and is expected to convert the type without any issue.
If this function is run at compile time, it is allowed to panic.

### 14.5.2. Builtin literal operators [↵](#145-literal-operators-)

Below is a list of the builtin literal operators:

literal operator | literal kind | resulting type | Info                                   | restrictions
-----------------|--------------|----------------|----------------------------------------|--------------
i8               | Integral     | i8             | 8-bit signed integer literal           | n/a
i16              | Integral     | i16            | 16-bit signed integer literal          | n/a
i32              | Integral     | i32            | 16-bit signed integer literal          | n/a
i64              | Integral     | i64            | 16-bit signed integer literal          | n/a
i128             | Integral     | i128           | 128-bit signed integer literal         | n/a
isize            | Integral     | isize          | machine-sized signed integer literal   | n/a
u8               | Integral     | u8             | 8-bit unsigned integer literal         | n/a
u16              | Integral     | u16            | 16-bit unsigned integer literal        | n/a
u32              | Integral     | u32            | 16-bit unsigned integer literal        | n/a
u64              | Integral     | u64            | 16-bit unsigned integer literal        | n/a
u128             | Integral     | u128           | 128-bit unsigned integer literal       | n/a
usize            | Integral     | usize          | machine-sized unsigned integer literal | n/a
f16              | Float        | f16            | 16-bit floating point literal          | n/a
f32              | Float        | f32            | 32-bit floating point literal          | n/a
f64              | Float        | f64            | 64-bit floating point literal          | n/a
f128             | Float        | f128           | 128-bit floating point literal         | n/a
b                | Character    | u8             | Byte character literal                 | n/a
b                | String       | &[u8]          | Byte string literal                    | n/a
c                | String       | cstr           | C-string literal (null-terminated)     | all characters are required to have a codpoint of <=0x7F
ansi             | String       | str8           | ANSI string literal                    | all characters are required to have a codpoint of <=0x7F
utf7             | String       | str16          | UTF-7 string literal                   | all characters are required to have a codpoint of <=0x7F
utf16            | String       | str16          | UTF-16 string literal                  | n/a
utf32            | String       | str32          | UTF-32 string literal                  | n/a

> _Note_: `Integral` means any of the following: DecLiteral, BinLiteral, OctLiteral or HexLiteral, and
>         `Float` means any of the following: DecFloatLiteral or HexFloatLiteral

For more info, see the [Operator](#14-operators-) section.

## 14.6. Operator scoping and use [↵](#1411-assginment-operators-)

```
<operator-use> := 'op' 'use' <use-root> [ '.' '{' <name> { ',' <name> }* [ ',' ] '}' ]
```

Operators have some special scoping rules, as they are not scoped relative to the module that contains them, but they are exclusivly at the global scope.
Only the actual operator set will be at a global level, but their respective traits will still be scoped like any other symbol.

Operators are imported using an `op use`, this will import all or specific operator sets from a given library into the global scope.
An imported operator set will always import all of its operators.

If any of the imported operator set would result in a duplicate operator, defined by it's punctuation and operator type, it will result in an error.

Unlike importing their associated traits, `op use`s are required to be within the main file of the library, i.e. in either the `main.mn` or `lib.mn` root, and must not be nested within a module in that file. 
One of the main purposes of this rule is to keep a consistent meaning of operators accross a library, i.e. avoiding a situation where an operator in 1 file has a different meaning than in another file, even if both are in the same library.

The core operators will be included by default via the core prelude.

# 15. Precedence [↵](#tables-of-contents)

Precedence defines the order in which expressions are evaluated, and is used to decide the order when multiple.
It is used to define which expressions have a higher priority than others, and those expression will be applied first.
Parentheses can be used to explictl change the order, as they have the highest precedence.

Another feature of precendence is the associativity.
When multiple expressions are chained, associativity defines which side has the higher 'precedence', i.e. how expressions are grouped together.

For example, the expression `a + b + c` can be written as either `(a + b) + c` or `a + (b + c)`.
While this doesn't always have an impact on the result generated, it should be assumed that the order can have an impact.
Each order could not only result in an actual difference in value, but even in type the expression will result, or in worse cases, fail to compile the underlying code.

For limitation on the naming, check the [precedence scoping and use](#153-precedence-scoping-and-use) section.

## 15.1. Built-in precedences [↵](#tables-of-contents)

The built-in precendences can be found in the table below, with the strongest at the to, and the weakest as the botton:

expressions                    | Associativity | Name          | After 
-------------------------------|---------------|---------------|--------
Parenthesized expressions      |               |               |
Path and literal expressions   |               |               |
Method call                    |               |               |
Field access                   |               |               |
Funtion calls                  |               |               |
Indexing                       |               |               |
Unary postfix operators        |               |               |
Unary prefix operators         |               |               |
Highest user-defined (no expr) |               | `Highest`     | n/a
Type cast/check                | left to right | `Typed`       | `Highest`
Power/Repetition               | left to rigth | `PowRep`      | `Typed`
Multiply/divide/remainder      | left to right | `MulDivRem`   | `PowRep`
Addition/Subtraction           | left to right | `AddSub`      | `MulDivRem`
Shift and rotate               | left to right | `ShiftRot`    | `AddSub`
Bitwise AND operations         | left to right | `BitAnd`      | `ShiftRot`
Bitwise XOR operations         | left to right | `BitXor`      | `BitAnd`
Bitwise OR operations          | left to right | `BitOr `      | `BitXor`
Or-else/err-coalesce           | left to right | `Select`      | `BitOr`
Comparison                     | left to right | `Compare`     | `Select`
Lazy boolean AND operators     | left to right | `LazyAnd`     | `Compare`
Lazy boolean OR operators      | left to right | `LazyOr`      | `LazyAnd`
range expression               | left to right | `Range`       | `LazyOr`
pipe operators                 | left to right | `Pipe`        | `Range`
Lowest user-defined (no expr)  |               | `Lowest`      | `Pipe`
Assingment expression          | right to left |

## 15.2. User-defined precedence [↵](#tables-of-contents)

```
<precedence-item> := 'precedence' <name> '{' { <precedence-member> }* '}'
<precedence-member> := 'higher_than' ':' <name>
                     | 'lower_than' ':' <name>
                     | 'associativity' ':' ( 'left' | 'right` | 'none' )
```

A precedence item can be used to define a custom precedence of user-defined operators.

### 15.2.1. Precendence order [↵](#152-user-defined-precedence-)

The item can decide which precedences must come before and after the new precedence, this can be defined by the `higher_than` and `lower_than` fields and refer to the name of other precendences.
The value given to `higher_than` must have a lower precedence than the item given to `lower_than`, and may not be the same.

It is allowed to have precedences form a non-linear precedence relation, but if 2 operators of different precendences that don't have a linear relation are used, they must be explicitly parenthesized, or this will result in a compilation error.

For example, if the precedences would result in the following relation
```
  A
 / \
B   |
|   D
C   |
 \ /
  E
```
operators of precendence `B` or `C` may not be used together with those of `D` without explicit parentheses, meaning that `v0 B v1 C v2` and `v0 B (v1 D v2)` are allowed, but not `v0 B v1 D v2` (where `B`, `C`, and `D` represent operators with those precendeces).

### 15.2.2. Associativity [↵](#152-user-defined-precedence-)

The associativity can also be defined, and can be set to `left`, `right`, or `none`.
This defines the resulting order of the expressions using these.
By default the value is set to `none`.

Associativity only comes into play when both operators have the same precedence, if they differ, they follow the rules defined above.

If the associativity is `left`, the expression will have a left-to-right order of evaluation.
For example, the expression `a + b + c` is represented as `(a + b) + c`.

If the associativity is `right`, the expression will have a right-to-left order of evaluation.
For example, if `+` would have had `right` associativity, the expression `a + b + c` is represented as `a + (b + c)`.

The `none` associativity requires explicit parentheses to be used.
For example, if `+` would have had `none` associativity, the expressions `(a + b) + c` and `a + (b + c)` would be valid, but `a + b + c` would be ambiguous and needs explicit parentheses.

Unary expression ignore associativity and go solely based on their precedence order.

## 15.3. Precedence scoping and use [↵](#15-precedence-)

```
<precedence-use> := 'precedence' 'use' <use-root> [ '.' '{' <name> { ',' <name> }* [ <name> ] '}' ] ';'
```

Precedences have some special scoping rules, as they are not scoped relative to the module that contains them, but they are exclusivly at the top level of a library.
This means that a library may not contain 2 precedences with the same name, no matter if they are in a nested module or not.

Precedences also are not imported from other files using a standard use declaration, but are instead imported by a special 'precedence use'.
Precedence uses declare a use root defining where the precedences are located, followed by an optional list of specific precedences to include.
Unlike precedence items which can be defined within a nested module, precedence uses are required to be within the main file of the library, i.e. in either the `main.mn` or `lib.mn` root, and must not be nested within a module in tht file.

When a precedence is imported, its name may not conflict with those of any other precedence declared within the library or imported from an external library.

# 16. Visibility [↵](#tables-of-contents)

```
<vis> := 'pub' [ '(' <vis-kind> ')' ]
<vis-kind> := 'package'
            | 'lib'
            | 'super'
            | 'in' <simple-path>
```

name resolution operates on a global hierarchy of names scopes.
Each level in the hierarchy can be seen as an item, this inludes items defined in the current library, but also those elsewhere in the package or external packages.

To control whether these can be used from accoress different locations, each item is checked for its availability in other scopes and whether these can be used or not.
If it is not available due to the items visibility, a compile error will be generated.

By default, everything is private, with 2 exceptions:
- Associated items in a `pub` trait are public
- Enum variants of a `pub` enum are also public by default.

When an item is declared as `pub`, it can be thought of as being accessible to the outside world.

With the notion of an item being private of public, items can be accessed in 2 cases:
- If an item is public, then it can be accessed externally from some module `m` if you can access all the item's ancester modules from `m`
   YOu can also potentially be able to name the item through re-exports.
- If an item is private, it may be accessed by the current module and its descendants.

## 16.1. Specifiers [↵](#16-visibility)

In addition to purely having items being purely private or public, items can also have a visibility spanning a specific scope, this is done with a specifier.
The following specifiers are available:
- `pub(package)`: Makes items visible inside of the current package
- `pub(lib)`: Makes item visible inside of the current library (no equivalent exists for binaries, as `pub` has the same effect in them, as they do cannot be used by another artifact)
- `pub(super)`: Makes the item visible inside of the parent's named scope.
- `pub(in path)`: Makes the item visible to the path specified (path is relative to the current artifact)

## 16.2. Common denominator [↵](#16-visibility)

The common denominator in mainly used for:
- [struct use fields](#use-fields-)

The common denominator of 2  visibilities is decided by choosing the narrowest visibility.
Meaning the visibilities will be decided by the first visibility in the followin order
1) 'private', i.e. no visibility specifier
2) `pub(in path)` or `pub(super)`
3) `pub(lib)`
4) `pub(package)`
5) `pub`

When the visibilities are both either `pub(in path)` or `pub(super)`, the following is done:
- convert the `super` specifier to an `in path` specifier
- pick the common root of the path
- If no common path is available, the visibility will be 'private'

# 17. Attributes [↵](#tables-of-contents)

```
<attribute> := '@' [ '!' ] <simple-path> [ '(' <attrib-meta> { ',' <attrib-meta> } [ ',' ] ')' ]
<attrib-meta> := <name>
               | <name> '=' <expr>
               | <name> '(' <attrib-meta> { ',' <attrib-meta> } [ ',' ] ')'
```

An attribute is general metadata that is given to the compiler, the resulting action depends on the attribute itself.
There are 2 types of attributes:
- module attributes starting with `@!`
- normal attributes starting with `@`

The difference between these attributes, is that the first one defined an attribute that is applied to the module it is in (or on the library if the file is a root module),
while the second applies to the item following it.

Expression may be used inside of attributes, but they cannot start using a name.

The following elements can have a attribute applied to them:
- All items
- Most statements
- Block expressions
- Enum variants
- Struct fields
- Match arms
- Function, function pointer, and closure paramters

> _TODO_: Explain how @attr(sub = "") and @attr(sub("")) are interchangable

## 17.1. Built-in attributes [↵](#17-attributes-)

Built-in attributes are attributes that the compiler can use to change its behavior.

### 17.1.1. Conditional compilation attributes [↵](#171-built-in-attributes-)

#### `cfg` [↵](#1711-conditional-compilation-attributes-)

The `cfg` attribute can be used to change the what code is compiled when certain configuration condtions are matched.
The `cfg` attribute is similar to the [`when` expression](#929-when-expressions), but is only allowed to access configuration values, these can be combined with lazy boolean operators and the not operator to define the condition for when the code should be compiled in.

#### `cfg_attr` [↵](#1711-conditional-compilation-attributes-)

The `cfg_attr` attribute can be used to change whether an attribute is applied when certain configurations are matched.
The `cfg_attr` is similar to the `cfg` attribute, but instead of being applied to the element below it, it has a second paramter containing the actual attribute that it represents

### 17.1.2. Derive attributes [↵](#171-built-in-attributes-)

#### `derive` [↵](#1712-derive-attributes-)

The `derive` attribute allows new items to automatically implement code for a given type, and is mainly used to implement attributes.
The derive contains a list of derive meta-attributes to derive.
Each derive meta-attribute may be supplied additional data that is attached to the meta-attribute.
For example `@derive(foo(bar = 1))`, passes the inner `bar = 1` to the derive attribute.


It contains a list of meta data with paths to builtin traits to implement or derive macros to process.

#### `auto_derive` [↵](#1712-derive-attributes-)

The `auto_derive` attribute is automatically added to any implementations generated by the `derive` attribute.
This attribute has no effect, but allows for tools and lints to detct that these have been automatically generated

### 17.1.3. Macro attributes [↵](#171-built-in-attributes-)

#### `meta_order` [↵](#1712-derive-attributes-)

The `meta_order` attribute is used to wrap [meta-attributes](#134-meta-attributes-) and control their order of execution.

The attribute contain the attribute to wrap, followed by a `before` and/or `after` argument with the names of the attribute that it should run before or after.

For example, `@meta_order(foo, before(bar), after(baz, quux))`, means that the `foo` attribute should run before `bar` is run, but after both `baz` and `quux`.

### 17.1.4. Diagnostic attributes [↵](#171-built-in-attributes-)

These attributes are used for controlling or generating diagnostic messages during compilation

#### `lint` attributes [↵](#1714-diagnostic-attributes-)

Linting attributes allows linters to check for potentially undesirable code patterns, such as unreachable code or omitted documentation.

The following lints attributes are supported:
- `allow(rule)`: overrides checks for `rule` and allows them to be treated as valid, so they are ignored.
- `warn(rule)`: Generates a warning whenever an occurance of `rule` is found, but continues compilation
- `deny(rule)`: Generates an error whenever an occurance of `rule` is found, and terminates compilation
- `forbid(rule)`: Similar to `deny`, but must be at a library level and forbids changing the lint level afterwards

The `rule`s used for these lint checks can be one of the standard compiler lints, or additional linter-specific rules.

Lint attributes are allowed to override the level specified by a previously define lint attribute, as lolng as the level does not try to change a forbidden lint.
Previous attributes are attributes defined in a higher level in the module hierarchy, or those passed directly to the compiler

##### Lint groups [↵](#lint-attributes-)

Lint attributes may be combined within lint groups, these have distint names and simultaniously set the lint level for all underlying attributes.
Lint groups can have their individual lint rules overriden by subsequent lint groups.

##### Tool lints [↵](#lint-attributes-)

A tool lints are scopes lint rules for certain tools.

Tool lints only get checked when their associated tools are active.
If a tool lint is encountered, but its tools is not active, they will be ignored

#### `available` & `unavailable` [↵](#1714-diagnostic-attributes-)

The `available` attribute defines since what minimum version of the library an item is available.
The `unavailable` attribute is the oposite of `available` and specifiers for which systems the feature is not available.

In addition, it may also define on which systems it is available and which system flags needs to be supported.

The attribute contains a version number, in addition to zero or more of the following sub-attributes and their respective configuration options:
sub-attribute | configuration option
--------------|----------------------
`arch`        | [`target_arch`](#221-target_arch-)
`arch_feats`  | [`target_features`](#222-target_feature-)
`os`          | [`target_os`](#223-target_os-)

#### `deprecated` [↵](#1714-diagnostic-attributes-)

The `deprecated` attributes allows items to marked as deprecated and will generate a warning on any use of it.

The `deprecated` attribute can be defined in multiple ways:
- `deprecated`: issues a generic message
- `deprecated("message")`: includes the given string in the deprecation message
- `deprecated(...)`: includes the given attributes in the deprecation message
    - `msg`: The main message
    - `note`: Additional notes for the deprecated item, can be used for to specify alternatives, or additional info why it was deprecated
    - `since`: Defines the semantic version of the library in which this item was deprecated.
    - `renamed`: Used to indicate which function should now be used, if it takes the same arguments. Tooling can use this to automatically fix any occurances of the function.

#### `obsolete` [↵](#1714-diagnostic-attributes-)

The `obsolete` attribute is similar to `deprecated`, but will instead of resulting in a warning, an error will be generated.

Likd `deprecated`, the `obsolete` attribute can be defined in the following ways:
- `obsolete`: issues a generic message
- `obsolete("message")`: includes the given string in the deprecation message
- `obsolete(...)`: includes the given attributes in the deprecation message
    - `msg`: The main message
    - `note`: Additional notes for the obsolete item, can be used for to specify alternatives, or additional info why it was obsolete
    - `since`: Defines the semantic version of the library in which this item was obsolete.
    - `renamed`: Used to indicate which function should now be used, if it takes the same arguments. Tooling can use this to automatically fix any occurances of the function.

#### `noasync` [↵](#1714-diagnostic-attributes-)

The `noasync` attribute notifies that a given function cannot be used inside of an async function.
The attribute can provide a message that explains why it cannot be used.

`async` function have no guarantee on which thread they will run, or that they will even complete on a single thread.
Therefore things like thread-local storage and locks can cause issues when transfered to a different thread.

#### `must_use` [↵](#1714-diagnostic-attributes-)

The `must_use` attribute will issue an warning or error (depending on the current lint level) when the resulting item is not used.
They can be defined on user-defined types and any kind of funtion.

When applied to a user-defined type, any return of a value of this type will result a message.
When applied to a function, if the return value of that function is not used, it will result in a message.

The `must_use` can return a generic message, or can be supplied with a message (`must_use("reason")`), which will print out the reason why the value must be used.

#### `diagnostics` [↵](#1714-diagnostic-attributes-)

The `diagnostics` attribute is a namespace of attributes that can affect compile time error reporting.
The hints provided by these attributes are not guaranteed to be used.
Unknown attributes in this namespace are accepted, though they may emit warnings for unsused attributes.

_TODO: Add diagnostics sub-attribs_

### 17.1.5. ABI, link, symbol, and FFI attributes [↵](#171-built-in-attributes-)

These attribute control how `extern` and `export` items will be managed

> _Note_: To control how a specific library is linked, use either command line options or a `build.mn` script

#### `link_prefix` [↵](#1715-abi-link-symbol-and-ffi-attributes-)

The `link_prefix` attribute set a common prefix for all link names whithin a block

#### `link_suffix` [↵](#1715-abi-link-symbol-and-ffi-attributes-)

The `link_suffix` attribute set a common suffix for all link names whithin a block

#### `link_name` [↵](#1715-abi-link-symbol-and-ffi-attributes-)

The `link_name` attribute is used to specify the link name of an external function or static.

#### `link_ordinal` [↵](#1715-abi-link-symbol-and-ffi-attributes-)

The `link_ordinal` can be used to specify the numeric ordinal of an external function or static.
The ordinal is a unique number identifying a symbol exported by a dynamic library on windows and can be used when the library is being loaded to find that symbol rather than having to look it up by name.

> _Warning_: The `link_ordinal` should only be used in cases where the ordinal of the symbol is stable: if the ordinal of a symbol is not explicitly set when its containing binary is built, then one will automitically be assigned to it, and that assigned oridinal may change between builds of the binary.

> _Note_: Not all libraries support ordinals

#### `repr` [↵](#1715-abi-link-symbol-and-ffi-attributes-)

The `repr` trait controls the type layout as defined in the [Layout representation section](#11412-layout-representation-)

#### `export_name` [↵](#1715-abi-link-symbol-and-ffi-attributes-)

The `export_name` attribute specifies the name of the symbol that will be exported on a function or static.

#### `link_section` [↵](#1715-abi-link-symbol-and-ffi-attributes-)

The `link_section` attribute specifies the section of the object file that a function of static's content will be placed into.

#### `no_mangle` [↵](#1715-abi-link-symbol-and-ffi-attributes-)

The `no_mangle` attribute disables name mangling and will output a symbol with the same name as  the function or static.

#### `used` [↵](#1715-abi-link-symbol-and-ffi-attributes-)

The `used` attribute can only be applied to static items.
This attribute is used to keep the variable in the output object file, even if the variable is not used or referenced by any other item inside the library.
However, the linker is still free to remove such an item.

#### `callconv` [↵](#1715-abi-link-symbol-and-ffi-attributes-)

The `callconv` attribute defined which calling convention a function will use when called.

#### `contextless` [↵](#1715-abi-link-symbol-and-ffi-attributes-)

The `contextless` attribute defines a function as running without access to the implicit context, maning that it will use the `contextless` ABI without having to be declared as [`extern` or `export`](#737-external-functions).
This means that this also can be applied on [methods](#735-method-).

### 17.1.6. Code generation attributes [↵](#171-built-in-attributes-)

Code generation attributes affect the resulting code generated by the compiler.
They give hints to the compiler to allow it to generate code that might be faster without these hints.
The compiler is free to ignore these hints

#### `builtin` [↵](#1716-code-generation-attributes-)

The `builtin` attributes attribute tells the compiler that the following element needs to be handled by the compiler, as it represents something which cannot be declared exclusivly within code.

#### `inline` [↵](#1716-code-generation-attributes-)

The `inline` attributes suggests taht the function should be placed inline in the caller, rather than generating a function call.
The following variations of the attribute are allowed:
- `inline`: suggest performing an inline expansion, i.e. strongly hints at it
- `inline(force)`: forces the compiler to always performing an inline expensions
- `inline(never)`: forces the compiler to never inline expansions

> _Note_: The compiler automatically inlines code based on a set of heuristics, these attributes apply modifiers to the heuristics on when to inline.
> Incorrect usage of this attribute may result in slower code, so should be used with care.

#### `cold` [↵](#1716-code-generation-attributes-)

The `cold` attribute suggest that the function is unlikely to be called.

#### `track_caller` [↵](#1716-code-generation-attributes-)

The `track_caller` attribute allows code within the function to get a hint of the `Location` of the top-most tracked call that leads to the function's invocation.
At the point of observation, an implementation behaves as if it walks up the stack from the function's frae to find the nearest frame of an unattributed function, and return the location of the tracked caller.

It can be applied to all `Minoa` ABI functions with the exception of the main function.
When applied to a function declaration inside of a trait, it will be applied to all implementations, if it is applied to a default implementation, it will also be applied to all overriding implementations.

#### `instruction_set` [↵](#1716-code-generation-attributes-)

The `instruction_set` attribute allows multiple identical function to be generated based on the instruction set being used in a program that can run multiple instructions sets on CPU architectures that support it.
An example of this is normal and thumb arm code.

#### `opt_level` [↵](#1716-code-generation-attributes-)

The `opt_level` attribute can be used to override the optimization level for a given functions.
This has the same possible values as the `opt_level` compiler setting.

#### `no_alias` [↵](#1716-code-generation-attributes-)

The `no_alias` attribute is applied to function parameters with a pointer or pointer-like types, guaranteeing that these do not alias and may therefore apply optimizations based on this fact.

#### `bit_size` [↵](#1716-code-generation-attributes-)

The `bit_size` attribute is used to explicitly define the bitsize of a type when used in a [bitfield](#78-bitfield-).
The attribute takes an integer literal value defining the bitwidth of a type in bits.

#### `field_prioity` [↵](#1716-code-generation-attributes-)

The `field_priority` attribute is used to define the priority of field within a `struct` with a Minoa representation, see [field priority](#field-priority).

#### `val_range` [↵](#1716-code-generation-attributes-)

The `val_range` attribute is used to define a range of valid value for any type that contains a single integer element.
This information can then be used for optimization by the compiler.

#### `autoclosure` [↵](#1716-code-generation-attributes-)

This marks an argument that receives a closure as being allowed to implicitly convert the value into closure.

#### `spec_priority` [↵](#1716-code-generation-attributes-)

The `spec_priority` attribute is uses in case there is a possible collision between specialization, see [resolving collisions](#resolving-collisions-)

#### `panic_handler` [↵](#1716-code-generation-attributes-)

The `panic_handler` attribute is used to specify a user provided panic handler.

#### `safety_check` [↵](#1716-code-generation-attributes-)

The `safety_check` attribute allows the control on whether safety check check should be generated for checkable illegal behaviors.
Safety check can can be either be set on at the top level, or on a category or sub-category level.

The following modes are supported:
- `on`: Safety checks will always generated
- `debug`: Safety checks will only generated in debug builds
- `off`: Safety checks are not generated

The default is `debug`.

Below is a table with mappings between `safety_check` attribute categories and their associated illegal behaviors

category  | sub-category    | illegal behavior
----------|-----------------|--------------------
`integer` |                 | [all integer IB](#231-integer)
`integer` | `truncation`    | [integer truncation](#2311-trunctation)
`integer` | `overflow`      | [integer overflow/underflow](#2312-overflowunderflow)
`integer` | `div_by_0`      | [division by 0](#2313-division-by-0)
`fp`      |                 | [all floating point IB](#232-floating-point)
`fp`      | `illegal_fp`    | [illegal operations](#2321-illegal-operations)
`fp`      | `fptoi_oob`     | [Floating-point to integer out-of-bounds](#2322-floating-point-to-integer-out-of-bounds)
`memory`  |                 | [all memory IB](#233-memory)
`memory`  | `out_of_bounds` | [Out-of-bounds](#2331-out-of-bounds)
`memory`  | `ptr_align`     | [Incorrect memory alignment](#2332-incorrect-pointer-alignment)
`memory`  | `sentinel`      | [](#2333-sentinel-access)
 
Categories and sub-categories may be set in addition to a more general value.

#### Examples [↵](#safety_check-)
```
@safety_check(.on) // Turn all check on
@safety_check(integer(div_by_0 = .on)) // Only turn integer division by 0 on

// Set the following
// - `.debug` for all safety checks, but
// - turn integer safety check to `.on`, but
// - turn integer overflow safety checks to `.off`
@safety_check(.debug, integer(.off, overflow = .on))
```

#### `fp_control` [↵](#1716-code-generation-attributes-)

The `fp_control` attribute allows control of how floating point operations are handled to set for a specific item, overwriting the default value for the program.

The possible controls are defined below.

> _Note_: Check the relavent section to see the supported architectures for each settings, if none are explicitly mentioned, the setting is available on all platforms

> _Note_: floating point controls can also be set at runtime

> _Note_: Setting floating point controls may prevent the compiler from fully inlining other function, or being inlined witin functions which have different `fp_control`s set

> _Todo_: If Bfloat16 is supported, at controls for ARM's EBF (extended brain float behaviors), and NEP (lowest element determination for SIMD) control bit

##### `exceptions` [↵](#fp_control-)

The `exceptions` control is a mask of flags that decide what floating point exceptions (and therefore IB) can be trigered.
If the exception can't be triggered, the instructions will return a value.

Below are the possible `exceptions` that can be set, and the default value that is returned when not.

`exceptions`  | value when off                             | meaning
--------------|--------------------------------------------|----------------
`.none`       | n/a                                        | Disable all flags
`.all`        | n/a                                        | Enables all flags
`.invalid_op` | `NaN`, depends on `nan_mode`               | a mathematically undefined operation, e.g. `sqrt(-1)`.
`.div_by_0`   | `+inf` or `-inf`, based on sign of operand | an operation on a finite operand that results in an exact infinite result, e.g. `1.0/0.0` or `log(0.0)`.
`.overflow`   | `+inf`                                     | a finite result is too large to be represented accurately (i.e. its exponent with an unbounded exponenet range would be larger that the maximum exponent).
`.underflow`  | subnormal, depends on `denormal`           | a result is very small (outsize of normal range).
`.inextact`   | Rounded value according to rounding mode   | the exact (i.e. unrounded) result is not represetable exactly.
`.denorm_in`  | n/a                                        | one of the operands passed to the instruction is a denormal value.

`exceptions` can be set to a combinations of flags, e.g. `.invalid_op | .div_by_0`

The default value of `exceptions` is `.all`

> _Note_: the `denormal_in` exception is only support on x86/64 processors

##### `rounding` [↵](#fp_control-)

The `rounding` control controls how values are rounded when there is not enough precision to store the full result, and can be set to the following:
- `.nearest`: Round towards nearest even value
- `.neg_inf`: Round towards -infinity
- `.pos_inf`: Round towards infinity
- `.zero`: Round towards zero

The default value of `rounding` is `.even`

##### `flush_to_zero` [↵](#fp_control-)

The `flush_to_zero` control decides what should happen when a denormal value is generated by an instructions, and can be set to the following values:
- `.save`: Keeps the denormal value
- `.flush`: Flushed any denormal value, i.e. set value to 0 when a denormal is generated. This mode is not IEEE compliant, but can provide performance improvements.

> _Note_: `flush_to_zero_half` is only supported on x86/64 and ARM

##### `flush_to_zero_half` [↵](#fp_control-)

The `flush_to_zero_half` control similar to `flush_to_zero`, expect that it defines this mode for half precision floatin points, and can be set to the following values:
- `.save`: Keeps the denormal value
- `.flush`: Flushed any denormal value, i.e. set value to 0 when a denormal is generated. This mode is not IEEE compliant, but can provide performance improvements.

> _Note_: `flush_to_zero_half` is only supported on ARM

##### `denormal_zero` [↵](#fp_control-)

The `denormal_zero` control controls how denormal operand to instructions should be interpreted, and can be set to the following values:
- `.denormal`: Passes the denormal to the operation as it is
- `.zero`: Inteprets any denormal input as 0. This mode is not IEEE compliant, but can provide performance improvements.

> _Note_: `flush_to_zero_half` is only supported on x86/64 and ARM

##### `precision` [↵](#fp_control-)

The `precision` control defines what precision x87 floating points do there calculations at, and can be set to the following:
- `.single`: Reduces x87 instruction to to 24-bit mantissa precision (f32 precision)
- `.double`: Reduces x87 instruction to to 53-bit mantissa precision (f64 precision)
- `.extended`: x87 instructions utilize the full 64-bit mantissa available (f80 precision)

The default value of `precision` is `.extended`

> _Note_: `precision` is only supported on x86/64, when x87 FPU instructions are used

##### `alt_half` [↵](#fp_control-)

The `alt_half` control defines whether an alternate version of half precision floating points is used, and can be set to the following:
- `.off`: Use the IEEE-754 rounding mode
- `.on`: Use ARM's alternate half fp format. This mode is not IEEE compliant.

The default value of `alt_half` is `.off`

On ARM, an alternate half is similar to a IEEE 754 half, but it does not support special values, instead uses those possible bitpatterns as valid values.

> _Note_: `alt_half` is only supported on ARM

##### `nan_mode` [↵](#fp_control-)

The `nan_mode` control defines how the input NaN value is passed through the operation, and can be set to the following:
- `.propagate`: Propagates/return the input NaN if provided, otherwise return the generated NaN.
- `.generate`: Return a default generated NaN. This mode is not IEEE compliant.

The default value of `nan_mode` is `.propagate`.

> _Note_: `alt_half` is only supported on ARM

##### `alt_handling` [↵](#fp_control-)

The `alt_handling` control defines wether the processor should use alternative handling for floating point numbers, and can be set to the following
- `.off`: Use standard handling
- `.on`: Use alternative handling. This mode is not IEEE compliant.

> _Note_: `alt_handling` is only supported on ARM, for more info, see the ARM architectural reference C5.2.8

### 17.1.7. Module attributes [↵](#171-built-in-attributes-)

These are module specific attributes

#### `path` [↵](#1717-module-attributes-)

The `path` attribute defines a path a module uses, as defined in [module path attribute section](#713-path-attribute-)

### 17.1.8. Debug attributes [↵](#171-built-in-attributes-)

Debug attributes allow for additional debug information to be specified for a given item.

#### `debugger_visualizer` [↵](#1718-debug-attributes-)

The `debugger_visualizer` attribute can be used to embed debugger visualizer info into the debugging information.
This enables an improved debugger experience for displaying values in the debugger.

The attribute exists out of a `kind` and either a `file` or `inline` specifier.

The `kind` specifier can be one of the following
- `natvis`: XML-based natvis for microsoft debuggers. More detail on the format can be found in Microsoft's [natvis documentation](https://learn.microsoft.com/en-us/visualstudio/debugger/create-custom-views-of-native-objects?view=vs-2022).
- `gdb`: GDB uses a python script based visualizer. More details on the format can be found in GDB's [pretty printing documentation](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Pretty-Printing.html).
- `minoa`: Minoa specific debug visualization (not supported yet)

The actual visualization can be specified in 2 ways:
- `file`: the visualization is specified in an internal file, this contains a path to it.
- `inline`: the visualization is specified inline inside of the code file

### 17.1.9. Documentation comments

#### `doc` [↵](#1719-documentation-comments)

The `doc` comment specifies a pseudo-attribute that represent [doc comments](#1719-documentation-comments).

## 17.2. Tool attributes [↵](#17-attributes-)

Tool attributes allow for external tools to supply its own attributes, with their own namespace

## 17.3. User-defined attributes [↵](#17-attributes-)

User-defined attributes are done using [meta attributes](#134-meta-attributes-).

In addition, result builders can also be used as the name in these attributes.

# 18. Implicit context [↵](#tables-of-contents)

Minoa passes an implicit context to all function and method calls and can be access in any one of them (assuming it uses the 'Minoa' ABI).

The context is passed to all function implicitly, and can be accessed from any valid locations.
All data in the context is immutable and can only be accessed via [interior mutability](#115-interior-mutability-).

Each member of the implicit context is stored within an optional, which by default will have a value of `.None`, and must be explicitly initialized by the program.
Since it's not possible to determine the exact order used to drop member (as libraries can add their own members), each member needs to be explicitly dropped by calling the explicit `.drop()` method on the member

The implicit context can be accessed via the `#context` meta-variable.

## 18.1 Defining context [↵](#18-implicit-context-)

Each libary is allowed to define any amount of additional context members, but they need to have unique names.

Context member can be defined in 2 ways:
- as a fixed type member
- as a trait member

A trait member can be defined from outside of the library adding it, a fixed type needs to be done via the library defining it.

## 18.2. Internals [↵](#18-implicit-context-)

The context is passed via a pointer in a fixed register, the context itself contains a number of nullable pointers to each individual member.
Members are accessed via a property.

Libraries define an external symbol, which is the index into the pointer array, while binaries define the final layout inside of the context and define the required symbols to access member correctly.

# 19. Effect system [↵](#tables-of-contents)
_TODO_

# 20. Contracts [↵](#tables-of-contents)

Contracts are used to find certain conditions that code needs to adhere to, these are generally split up in 2 main types.

Constracts evaluation happens in the following order:
1. Check if the contract group is active, if not, stop.
2. Check if the contract group has a predicate, and if it evaluates to `false`, stop.
3. Check the condition inside of the contract, if it evaluates to `false`, stop.
4. Finally report the validation via the contract group.

> _Note_: The exact API of contract groups still needs to be determined

## 20.1. Function contracts [↵](#20-contracts-)

```
<fn-contract>             := <pre-contract> | <post-contract> | <invar-contract>
<pre-contract>            := 'pre' [ <contract-group-and-pred> ] '(' <expr> ')'
<post-contract>           := 'post' [ <contract-group-and-pred> ] '(' [ <name> '=>' ]  <expr> ')'
<invar-contract>          := 'invar' [ <contract-group-and-pred> ] '(' <expr> ')'
<contract-group-and-pred> := '[' <expr> [ 'if' <expr> ] ']'
                           | '[' 'if' <expr> ']'
```

Function contracts are composed out of 3 different kinds:
- preconditions
- postconditions
- Invariant conditions

A preconditions is used to define what values may be passed into a function.
Preconditions are evaluated before the function body gets executed.
For example what range an integer value should be in.

A postconditions is used to to check if the resulting state at the end of the function.
Postconditions may access unnamed return values by prepending the condition with `name =>`.
Postconditions are evaluated at after the function body, but before the function returns.
For example, checking if an a value was set to a value in a given range.

Postconditions also allow use of the contract capture operator to capture a value at the start of a function to use in the contract.

An invariant conditions is used to check the invariance of certain conditions, meaning that they cannot change the result of this condition over the functions lifetime.
Invariant conditions are evaluated when pre- or postconditions are evaluated.

Contracts may make use of the [contract capture operator](#1424-contract-capture-operator-)

## 20.2. Asserts [↵](#20-contracts-)

```
<assert> := [ 'const' ] 'assert' [ <contract-group-and-pred> ] '(' <expr> { ',' <expr> }* ')' ';'
```

An assert is a special condition which may be used at any moment in code to check if a value adheres to given conditions.
They can be evaluated both at runtime or compiletime.

## 20.3. Contract groups [↵](#20-contracts-)

Contract groups are used to manage the evaluation of a contracts.
The allow entire contracts to be disable, under which conditions they need to be evaluated, and how they should report an error.

Contract groups can be specified between `[` and `]` in an assert.
If no contract group is specified, the default contract groups is used, which has the following state:
- Only active when assertions are enabled via the assert configuration option
- Has no predicate, i.e. will always be checked
- Panics on a contract violation

> _Note_: The exact API of contract groups still needs to be determined.
> It also still needs to be determined how to override the default contract group.

## 20.4. Constact predicates

In addition to declaring a contract group, each individual contract and assert may also have a predicate that needs to result in a `true` value to be able to run.
These are compile time expressions.

The are located together with a contract group, but are set after an `if`.

## 20.5. Testing

Contract groups are also used for testing and are hooked into by the testing framework.

> _Note_: The testing framework has not entirely been figured out yet

# 21. ABI [↵](#tables-of-contents)
> _Todo_

# 22. Configuration options [↵](#tables-of-contents)

Configuration options can be used inside [conditional compilation attributes](#22-configuration-options) and the [`when` expressions](#929-when-expressions).

The possible configuration options are generated per-project and may be extended past the built-in values by compilation set extensions (_TODO: link to compiler docs_).

> _Note_: This section contains the currently supported and planned values, some may not be supported yet

## 22.1. `target_arch` [↵](#22-configuration-options-)

This value defines which architecture the code is being compiled for.

Architecture | Description
-------------|-------------
`.interp`    | interpreter
`.x64`       | x86_64
`.aarch64`   | 64-bit arm (currently unsupported)
`.riscv`     | riscv (currently unsupported)

## 22.2. `target_feature` [↵](#22-configuration-options-)

Defines the features available on the current architecture.
If a feature for a differen architecture is used then is allowed in the current scope, an error will be returned.

Tools should generally only show the target features of architectures a that are available within the scope

### 22.2.1. x86/x64 (x86_64) [↵](#222-target_feature-)

Feature               | Implicit features | Description
----------------------|-------------------|-------------
`.adx`                |                   | [ADX](https://en.wikipedia.org/wiki/Intel_ADX) - multi-precision ADd-carry instruction eXtensions
`.aes`                | `.sse2`           | [AES](https://en.wikipedia.org/wiki/AES_instruction_set) - Advanced Encryption Standard
`.avx`                | `.sse4_2`         | [AVX](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions) - Advanced Vector eXtensions
`.avx2`               | `.avx`            | [AVX2](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX2) - Advanced Vector eXtensions 2
`.avx512f`            | `.avx2`           | [AVX512F](https://en.wikipedia.org/wiki/AVX-512) - Advanced Vector eXtensions 512 - Foundation
`.avx512cd`           | `.avx512f`        | [AVX512CD](https://en.wikipedia.org/wiki/AVX-512#Conflict_detection) - Advanced Vector eXtensions 512 - 
`.avx512er`           | `.avx512f`        | [AVX512ER](https://en.wikipedia.org/wiki/AVX-512#Exponential_and_reciprocal) - Advanced Vector eXtensions 512 - 
`.avx512pf`           | `.avx512f`        | [AVX512PF](https://en.wikipedia.org/wiki/AVX-512#Prefetch) - Advanced Vector eXtensions 512 - 
`.avx512vl`           | `.avx512f`        | [AVX512VL](https://en.wikipedia.org/wiki/AVX-512) - Advanced Vector eXtensions 512 - Vector Length
`.avx512dq`           | `.avx512f`        | [AVX512DQ](https://en.wikipedia.org/wiki/AVX-512#BW,_DQ_and_VBMI) - Advanced Vector eXtensions 512 - Doubleword and Quadword
`.avx512bw`           | `.avx512f`        | [AVX512BW](https://en.wikipedia.org/wiki/AVX-512#BW,_DQ_and_VBMI) - Advanced Vector eXtensions 512 - Byte and Word
`.avx512ifma`         | `.avx512f`        | [AVX512IFMA](https://en.wikipedia.org/wiki/AVX-512#IFMA) - Advanced Vector eXtensions 512 - Integer Fused Multiply Add
`.avx512vbmi`         | `.avx512f`        | [AVX512VBMI](https://en.wikipedia.org/wiki/AVX-512#BW,_DQ_and_VBMI) - Advanced Vector eXtensions 512 - Vector Byte Manipulation Instructions
`.avx512_4vnni`       | `.avx512f`        | [AVX512_4VNNI](https://en.wikipedia.org/wiki/AVX-512#4FMAPS_and_4VNNIW) - Advanced Vector eXtensions 512 - Vector Neural Network Instrauction Word variable precision
`.avx512_4fmaps`      | `.avx512f`        | [AVX512_4FMAPS](https://en.wikipedia.org/wiki/AVX-512#4FMAPS_and_4VNNIW) - Advanced Vector eXtensions 512 - Fused Multiply Add packed single precision
`.avx512vpopcntdq`    | `.avx512f`        | [AVX512VPOPCNTDQ](https://en.wikipedia.org/wiki/AVX-512#VPOPCNTDQ_and_BITALG) - Advanced Vector eXtensions 512 - Vector POPulation CouNT
`.avx512vnni`         | `.avx512f`        | [AVX512VNNI](https://en.wikipedia.org/wiki/AVX-512#VNNI) - Advanced Vector eXtensions 512 - Vector Neural Network Instructions
`.avx512vbmi2`        | `.avx512f`        | [AVX512VBMI2](https://en.wikipedia.org/wiki/AVX-512#VBMI2) - Advanced Vector eXtensions 512 - Vector Byte Manipulation Instructions 2
`.avx512bitalg`       | `.avx512f`        | [AVX512BITALG](https://en.wikipedia.org/wiki/AVX-512#VPOPCNTDQ_and_BITALG) - Advanced Vector eXtensions 512 - BIT ALGorithms
`.avx512vp2intersect` | `.avx512f`        | [AVX512VP2INTERSECT](https://en.wikipedia.org/wiki/AVX-512#VP2INTERSECT) - Advanced Vector eXtensions 512 - 
`.avx512gfni`         | `.avx512f`        | [AVX512GFNI](https://en.wikipedia.org/wiki/AVX-512#GFNI) - Advanced Vector eXtensions 512 - Galois Field New Instructions
`.avx512vpclmulqdq`   | `.avx512f`        | [AVX512VPCLMULQDQ](https://en.wikipedia.org/wiki/AVX-512#VPCLMULQDQ) - Advanced Vector eXtensions 512 - Vector Carry-Less Multiply Quadword
`.avx512veas`         | `.avx512f`        | [AVX512VEAS](https://en.wikipedia.org/wiki/AVX-512#VAES) - Advanced Vector eXtensions 512 - Vector AES instructions
`.avx512BF16`         | `.avx512f`        | [AVX512BF16](https://en.wikipedia.org/wiki/AVX-512#BF16) - Advanced Vector eXtensions 512 - BFloat16
`.avx512FP61`         | `.avx512f`        | [AVX512FP16](https://en.wikipedia.org/wiki/AVX-512#FP16) - Advanced Vector eXtensions 512 - Half-Precision floating point
`.bmi1`               |                   | [BMI1](https://en.wikipedia.org/wiki/X86_Bit_manipulation_instruction_set#BMI1) - Bit Manipulation Instructions set 1
`.bmi2`               |                   | [BMI2](https://en.wikipedia.org/wiki/X86_Bit_manipulation_instruction_set#BMI2) - Bit Manipulation Instructions set 2
`.cmpxchg16`          |                   | [cmpxchg16](https://www.felixcloutier.com/x86/cmpxchg8b:cmpxchg16b) CoMPare and eXCHange 16 Bytes (128-bits) of data atomically
`.f16c`               | `.avx`            | [F16C](https://en.wikipedia.org/wiki/F16C) - 16-bit Floating point Conversion instructions
`.fma`                | `.avx`            | [FMA3](https://en.wikipedia.org/wiki/FMA_instruction_set) - 3-operand Fused Multiply-Add
`.fxsr`               |                   | [fxsave](https://www.felixcloutier.com/x86/fxsave) and [fxrstor](https://www.felixcloutier.com/x86/fxrstor) - Save and restore x87 FPU, MMX technology and SSE state
`.lzcnt`              |                   | [lzcnt](https://www.felixcloutier.com/x86/lzcnt) - Leading Zero CouNT
`.movbe`              |                   | [movbe](https://www.felixcloutier.com/x86/movbe) - MOVe data after swapping bytes
`.pclmulqdq`          | `.sse2`           | [pclmulqdq](https://www.felixcloutier.com/x86/pclmulqdq) - Packed Carry-Less Multiply Quadword
`.popcnt`             |                   | [popcnt](https://www.felixcloutier.com/x86/popcnt) - POPulation CouNT
`.rdrand`             |                   | [rdrand](https://en.wikipedia.org/wiki/RDRAND) - ReaD RANDom number
`.rdseed`             |                   | [rdseed](https://en.wikipedia.org/wiki/RDRAND) - ReaD random SEED
`.sha`                | `.sse2`           | [SHA](https://en.wikipedia.org/wiki/Intel_SHA_extensions) - Secure Hash Algorith
`.sse`                |                   | [SSE](https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions) - Streaming SIMD Extensions
`.sse2`               | `.sse`            | [SSE2](https://en.wikipedia.org/wiki/SSE2) - Streaming SIMD Extensions 2
`.sse3`               | `.sse2`           | [SSE3](https://en.wikipedia.org/wiki/SSE3) - Streaming SIMD Extensions 3
`.sse4_1`             | `.ssse3`          | [SSE4.1](https://en.wikipedia.org/wiki/SSE4#SSE4.1) - Streaming SIMD Extensions 4.1
`.sse4_2`             | `.sse4_2`         | [SSE4.2](https://en.wikipedia.org/wiki/SSE4#SSE4.2) - Streaming SIMD Extensions 4.2
`.ssse3`              | `.sse3`           | [SSSE3](https://en.wikipedia.org/wiki/SSSE3) - Supplemental Streaming SIMD Extensions 3
`.xsave`              |                   | [xsave](https://www.felixcloutier.com/x86/xsave) - SAVE processor eXtended state
`.xsavec`             |                   | [xsavec](https://www.felixcloutier.com/x86/xsavec) - SAVE processor eXtended states with Compaction
`.xsaveopt`           |                   | [xsaveopt](https://www.felixcloutier.com/x86/xsaveopt) - SAVE processor eXtended state OPTimized
`.xsaves`             |                   | [xsaves](https://www.felixcloutier.com/x86/xsaves) - SAVE processor eXtended sate Supervisor


> _Note_: this list may be incomplete

### 22.2.1. arm/aarch64 [↵](#222-target_feature-)

_TODO_

### 22.2.3. riscv [↵](#222-target_feature-)

_TODO_

## 22.3. `target_os` [↵](#22-configuration-options-)

This value defines the operating system the code is being compiled for:
- `.windows`
- `.linux`

## 22.4. `target_endianness` [↵](#22-configuration-options-)

This value defines the endianness of the target:
- `.little`
- `.big`

## 22.5. `target_pointer_width` [↵](#22-configuration-options-)

This value defines the pointer width on the target:
- `32`
- `64`

## 22.6. `compilation_mode` [↵](#22-configuration-options-)

This value defines the compilation mode:
- `.debug`
- `.release`

> _Note_: Additional values can be provided to the compiler

## 22.7. `assertions` [↵](#22-configuration-options-)

This value defines whether assertions are enabled:
- `.on`
- `.off`

## 22.9. `panic` [↵](#22-configuration-options-)

This value defined the panic mode
- `.unwind`
- `.abort`

# 23. Illegal behavior [↵](#tables-of-contents)

Minoa has many operations which result in illegal behavior (IB). This can be compared to languages which have undefined beharvior (UB), illegal behavior is explicitly defined as invalid and will result in errors, instead of strange runtime behavior.

If illegal behavior is detected at compile time, the compiler will emit a compiler error and stop compiling.
If instead it happens during runtime, it will fall into one of two categories: safety checked and unchecked.

Unchecked illegal behavior, means that the compiler is not able to insert safety check for it. When this behavior is invoked at runtime, it's behavior is undefined.
The optimizer is free to make this illegal behavior do anything, which could lead from thing as simple as crashing, but may also execute code that should not be called, or even overwrite arbitrary data.
If unchecked behavior is executed at compile time, it will emit a compiler error, as it has much better infrastructure to catch issues like these.

Safety-checked illegal behaviors means that the compiler is able to insert a safety check of whereever this behavior can occur. At runtime, this will result in the program panicking.
The majority of illegal behavior is of this type.

For performance reasons, these safety checks may be enabled or disabled on a granular level, overriding the default for the current compilation mode.
This granularity works in 2 ways:
- on a safety check category level, and
- on a code level, from the entire library to statements.

This can be done using [safety-check attributes](#1717-safety-checks-)

> _Todo_: Add support for IB from contracts

> _Todo_: Add more examples: compile time & runtime

## 23.1. Integer [↵](#23-illegal-behavior-)

Illegal behavior on integer operations can be caused by the below categories.

### 23.1.1. Trunctation [↵](#231-integer-)

There are 2 ways of casting an integer that cause truncation:
- casting from a negative signed integer to a signed integer value
- casting a value of a larger bitwidth to a smaller bitwidth integer that cannot contain the value

#### Examples
```
// Casting a negative value to an unsigned type
let a: i32 = -23;
a as u32;

// Casting to a smaller bitwidth that cannot contain the value
let b: i16 = 300;
a as i878
```

To truncate a value, use the `<todo: insert fn name here>` function.

### 23.1.2. Overflow/underflow  [↵](#231-integer-)

An integer value can overflow or underflow the possible value that can be contained within the integer type.
The following operators can cause an overflow or underflow:
- infix `+` (additon)
- infix `-` (subtraction)
- prefix `-` (negation)
- infix `*` (multiplication)
- infix `/` (subtraction)

To avoid overflow, either the wrapping, saturating or try variants of the operators could be used, or the associated core `..._with_overflow` function can be used.

> _Todo_: Should we add core functions here that might cause it too?

### 23.1.3. Division by 0  [↵](#231-integer-)

An integer value division by 0 can be caused by the following operators:
- infix `/` (division) 
- infix `%` (remainder)

## 23.2. Floating point [↵](#23-illegal-behavior-)

Illegal behavior of floating point operations can be caused by the below category

### 23.2.1. Illegal operations [↵](#232-floating-point-)

Illegal floating point operations are operation that produce floating point exceptions.
These are slightly different to other illegal behavior, as they are actually defined within the [IEEE 754 specification](https://en.wikipedia.org/wiki/IEEE_754).

They are caused by operation that result in on of the following things:
- invalid operation: a mathematically undefined operation, e.g. `sqrt(-1)`
- division by 0: an operation on a finite operand that results in an exact infinite result, e.g. `1.0/0.0` or `log(0.0)`
- overflow: a finite result is too large to be represented accurately (i.e. its exponent with an unbounded exponenet range would be larger that the maximum exponent).
- underflow: a result is very small (outsize of normal range)
- inexact: the exact (i.e. unrounded) result is not represetable exactly.

In addition, some processors can also produce the following exception:
- denormal opearand: one of the operands passed to the instruction is a denormal value.

In addition, this also applies to certain functions when either operand is one of the following values
- `+inf`
- `-inf`
- `NaN` (quiet and signalling)

Or when their result does not fit in the destination size.

> _Note_: These illegal behavior depend on the floating point settings by the program.

> _Todo_: specify which functions can additionally cause FP exceptions

### 23.2.2. Floating-point to integer out-of-bounds [↵](#232-floating-point-)

A floating point value can be out of bounds when trying to convert it to an integer value,
meaning that the value in the float cannot be converted to a valid integer value.

#### Examples
```
let f: f32 = 4294967296;
let i: i32 = f as i32;
```
## 23.3. Memory [↵](#23-illegal-behavior-)

Illegal behavior on memory can be cause by the below categories

### 23.3.1. Out-of-bounds [↵](#233-memory-)

Indexing memory out of bounds, thus resulting accessing an invalid memory location, is illegal behavior.
This can be caused by indexing into an array or a slice.

### 23.3.2. Incorrect pointer alignment [↵](#233-memory-)

When assigning a memory address to a pointer, that memory address may not adhere to the alignment requirements of the pointer.
This also applies to references.

### 23.3.3. Sentinel access [↵](#233-memory-)

When an array, slice, or multi-element pointer has a sentinel value, they can be written to and read from past the sentinel value.

# 24. Language builtins [↵](#tables-of-contents)

A language builtins, also known as language items, are special items that the compiler understands as having a special meaning.
All these types are implemented in the `core` library.

These are define either by `@builtin(...)` attribute or as builtin metafunctions.

Each langauge builtin has a corresponding builtin name.

## 24.1. Traits [↵](#24-language-builtins-)

### 24.1.1. `Clone` [↵](#241-traits-)

The corresponding builtin name is: `clone`

`Clone` is a supertrait for `Copy`

`Clone` is also used in the generation of record.

### 24.1.2. `Copy` [↵](#241-traits-)

The corresponding builtin name is `copy`
This has `Clone` as a supertrait.

`Copy` indicates to the compiler that the type may be copies using a byte-wise memcpy.

`Copy` is only allowed to be implemented when the type:
- implements its supertrait [`Clone`](#241-clone-)
- does not manually implement [`Clone`](#241-clone-)
- All its fields/elements implement `Copy`

`Copy` is also used in the generation of record types.

### 24.1.3. `Equality` [↵](#241-traits-)

The corrsponding builtin name is `equality`.

For more info, see [comparison operators](#1431-comparison-operators-)

### 24.1.4. `Identity` [↵](#241-traits-)

The corresponding builtin name is `identity`.

For more info, see [comparison operators](#1431-comparison-operators-)

### 24.1.5. `Ord` [↵](#241-traits-)

The corresponding builtin name is `ord`.

For more info, see [comparison operators](#1431-comparison-operators-)

### 24.1.6. `TotalOrd` [↵](#241-traits-)

The corresponding builtin name is `total_ord`.

For more info, see [comparison operators](#1431-comparison-operators-)

### 24.1.7. `StructuralEquality` [↵](#241-traits-)

The corresponding builtin name is `structural_equality`.
This has `Equality` as a super trait.

This is an `unsafe` trait.

It is used to indicate to pattern matching that a constant of the implementing type can be used as a constant in pattern matching.
When a constant of this type is encountered, it will use `Equality::eq` to compare if the values are the same.

`StructuralEquality` indicates a struct is compared as follows:
- All fields are individually checked

This trait is mutually exclusive with `StructuralIdentity`.

It requires that `Equality` is implemented as `const`, i.e. it can run its functions at compile-time.

### 24.1.8. `StructuralIdentity` [↵](#241-traits-)

The corresponding builtin name is `structural_identity`
This has `Identity` as a super trait.

This is an `unsafe` trait.

It is used to indicate to pattern matching that a constant of the implementing type can be used as a constant in pattern matching.
When a constant of this type is encountered, it will use `Identity::identity` to compare if the values are the same.

This trait is mutually exclusive with `StructuralEquality`.

It requires that `Identity` is implemented as `const`, i.e. it can run its functions at compile-time.

### 24.1.9. `OptAccess` [↵](#241-traits-)

The corresponding builtin name is `opt_access`.

This is used to indicate that any null-propagating expression may be performed on the type.

### 24.1.10. `DispatchFromDyn` [↵](#241-traits-)

The corresponding builin name is `dispatch_from_dyn`.

This trait indicates that the type may be used to wrap a reciever.

This differs between implementing a `Deref`-style interface, as it only allows it to be called when the specific type `U` wraps the implementing type `T`.
This requires that `U` implements `DispatchFromDyn(T)`.

### 24.1.11. `From` [↵](#241-traits-)

The corresponding builtin name is `from`.

This trait corresponds to a call to `as`.

It works in tandem with the `To` trait, only one of them may be manually implemented.
Implementing either trait will implicitly implement the other.

### 24.1.12. `TryFrom` [↵](#241-traits-)

The corresponding builtin name is `try_from`.

This trait corresponds to a call to `as?`.

It works in tandem with the `TryTo` trait, only one of them may be manually implemented.
Implementing either trait will implicitly implement the other.

### 24.1.13. `UnwrapFrom` [↵](#241-traits-)

The corresponding builtin name is `try_err_from`.

This trait corresponds to a call to `as!`.

It works in tandem with the `UnwrapTo` trait, only one of them may be manually implemented.
Implementing either trait will implicitly implement the other.


### 24.1.14. `Default` [↵](#241-traits-)

The corresponding builtin name is `default`.

This trait indicates a default value for a type.
Unlike default fields, it can use runtime code.

### 24.1.15. `Index` [↵](#241-traits-)

The corresponding builtin name is `index`.

This trait defines the index expression operator, i.e. `a[b]`, taking in `&self`, and returning `T`..

### 24.1.16. `IndexMut` [↵](#241-traits-)

The corresponding builtin name is `index_mut`.

This trait defines the mutable index expression operator, i.e. `a[b]`, taking in `&mut self`, and returning `T`..

### 24.1.17. `OptIndex` [↵](#241-traits-)

The corresponding builtin name is `opt_index`.

This trait defines the optional index expression operator, i.e. `a[?b]`, taking in `&self`, and returning `?T`..

### 24.1.18. `OptIndexMut` [↵](#241-traits-)

The corresponding builtin name is `opt_index_mut`.

This trait defines the mutable index expression operator, i.e. `a[?b]`, taking in `&mut self`, and returning `?T`.

### 24.1.19. `RevIndex` [↵](#241-traits-)

The corresponding builtin name is `rev_index`.

This trait defines the index expression operator, i.e. `a[^b]`, taking in `&self`, and returning `T`..

### 24.1.20. `RevIndexMut` [↵](#241-traits-)

The corresponding builtin name is `rev_index_mut`.

This trait defines the mutable index expression operator, i.e. `a[^b]`, taking in `&mut self`, and returning `T`..

### 24.1.21. `RevOptIndex` [↵](#241-traits-)

The corresponding builtin name is `rev_opt_index`.

This trait defines the optional index expression operator, i.e. `a[?^b]`, taking in `&self`, and returning `?T`..

### 24.1.22. `RevOptIndexMut` [↵](#241-traits-)

The corresponding builtin name is `rev_opt_index_mut`.

This trait defines the mutable index expression operator, i.e. `a[?^b]`, taking in `&mut self`, and returning `?T`.

### 24.1.23. `Fn` [↵](#241-traits-)

The corresponding builtin name is `fn`.

This trait indicates that a type may be called as a function.
The type is accessed as `&self`, and may thus not modify its own state.

### 24.1.24. `FnMut` [↵](#241-traits-)

The corresponding builtin name is `fn_mut`.

This trait indicates that a type may be called as a function.
The type is accessed as `&mut self`, and may thus modify its own state.

### 24.1.25. `FnOnce` [↵](#241-traits-)

The corresponding builtin name is `fn_once`.

This trait indicates that a type may be called as a function.
The type is accessed as `self`, and is therefore moved.

### 24.1.26. `IntoIterator` [↵](#241-traits-)

The corresponding builtin name is `into_iterator`.

This trait indicates that a type which can be converted into an iterator to be able to be used in a [`for` expression](#9194-for-expression-).

### 24.1.27. `Iterator` [↵](#241-traits-)

The corresponding builtin name is `into_iterator`.

This trait indicates that a type is iterable and can be used inside of a 

### 24.1.28. `Try` [↵](#241-traits-)

The corresponding builtin name is `try`.

This trait allows for a type to have a [`try` expression](#927-try-expression-).
This also defines the postfix `?` (try) operator.

### 24.1.29. `TryUnwrap` [↵](#241-traits-)

The corresponding builtin name is `try_unwrap`.

This trait allows for a type to have a [`try!` expression](#927-try-expression-) be called on it.
This also defines the postfix `!` (unwrap) operator.

### 24.1.30. `Catch` [↵](#241-traits-)

The corresponding builtin name is `catch`.

This trait is used to check for an erronous value when calling a catch expression on it.
It is also defines the `??` operator.

### 24.1.31. `Hash` [↵](#241-traits-)

The corresponding builtin name is `hash`.

This builtin only exists to allow the compiler to automatically generate a hashing function for structural types.

### 24.1.32. `Sized` [↵](#241-traits-)

The corresponding builtin name is `sized`.

This trait marks all types that have a known size.

### 24.1.33. `Unsize` [↵](#241-traits-)

The corresponding builtin name is `unsize`.

This trait indicates if a type may be coerced to which unsized types.
It is used in tandem with [`CoerceUnsized`](#24133-coerceunsized-)

### 24.1.34. `CoerceUnsized` [↵](#241-traits-)

The corresponding builtin name is `coerce_unsized`.

This trait allows types that contain an `Unsize` type to be coerced into a type containing its unsized version, i.e. `T(U is Unsize(V))` -> `T(V)`

### 24.1.35. `Drop` [↵](#241-traits-)

The corresponding builtin name is `drop`.

This trait defines how a type should be dropped when going out of scope.

### 24.1.36. `Borrow` [↵](#241-traits-)

The corresponding builitn name is `borrow`

This trait defines the `&` operator, but also indicates to the compiler that the value has had a shared borrow taken.

### 24.1.37. `BorrowMut` [↵](#241-traits-)

The corresponding builitn name is `borrow_mut`

This trait defines the `&mut` operator, but also indicates to the compiler that the value has had a shared borrow taken.

### 24.1.28. `Deref` [↵](#242-types-)

The corresponding builtin name is `deref`.

This trait implements the dereference operator.
It is also used by the compiler to keep track of borrowing.

### 24.1.39. `DerefMut` [↵](#242-types-)

The corresponding builtin name is `deref_mut`.

This trait implements the dereference operator.
It is also used by the compiler to keep track of borrowing.

### 24.1.40. `DerefMove` [↵](#242-types-)

The corresponding builtin name is `deref_move`.

This trait implements the dereference operator.
It is also used by the compiler to keep track of borrowing.

### 24.1.41. `LogicalAnd` [↵](#241-traits-)

The correspodning builtin name is `logical_and`

This trait implements the `&&` opeator.
It also is used to manage let-binding chains.

### 24.1.42. `LogicalOr` [↵](#241-traits-)

The correspodning builtin name is `logical_and`

This trait implements the `&&` opeator.
It also is used to manage let-binding chains.

### 24.1.44. `ResultBuilder` [↵](#241-traits-)

The corresponding language item is `result_builder`.

Result builder allow types to be constructed in a much more ergonomic way.
It allows a closure with a comma seperated end-expression to be converted into code using the result builder.

The closures using a result builder are annotated using an attribute with the result builder's type as their name.

## 24.2. Types [↵](#24-language-builtins-)

### 24.2.1. `Option` [↵](#242-types-)

The corresponding builtin name is `option`.

This represents the desugared [nullable type](#11114-optional-types-), which wraps another type and allows it to be `.None`

### 24.2.2. `Result` [↵](#242-types-)

The corresponding builtin name is `result`.

This represents the desugared [result type](#11115-result-types-), allowing a value to be valid or have an error value

### 24.2.3. `KeyValue` [↵](#242-types-)

The corresponding builtin name is `key_value`

This type stored key-value pairs generated by key-value array list expressions or array comprehensions.

### 24.2.4. `KeyPath` [↵](#242-types-)

The corresponding builtin name is `key_path`.

This types stores the value created by a [key-path expression](#932-key-path-expressions-).

### 24.2.5. `ManuallyDrop` [↵](#242-types-)

The corresponding builtin name is `manually_drop`.

This type can wrap another type and prevent its `Drop.drop()` function from being called when it goes out of scope.

### 24.2.6. `MaybeUninit` [↵](#242-types-)

The corresponding builtin name is `maybe_uninit`.

This types wraps any type and allows it to be in an uninitialized state.
This is not illegal behavior, as the compiler knows that the underlying data is in an invalid state.

To be able to use the value wrapped by `MaybeUninit`, it needs to explicitly be declared as fully initialized.

Like `ManuallyDrop`, any value wrapped within this type is **not** automatically dropped.

> _Todo_: There might be compiler checks in the future that ensures all member are initialized.

### 24.2.7. `InterplatedString` [↵](#242-types-)

The corresponding builtin name is `interpolated_string`.

This type is used to represent any string which contains interpolations, where the expressions provided will be treated as lazy values.

### 24.2.8. `meta.TokenStream` [↵](#242-types-)

The corresponding builtin name is `meta_token_stream`.

This type represents a compiler provided token stream used by meta-functions to receive and return arbitrary code.

### 24.2.9. `meta.FragmentStream` [↵](#242-types-)

The corresponding builtin name is `meta_fragment_stream`.

This type represents the input for pattern parse and pattern match functions.

### 24.2.10. `meta.Ast` [↵](#242-types-)

The corresponding builtin name is `meta_ast`.

This type represents an AST sub-tree used by meta-functions to receive and return arbitrary code.

### 24.2.11. `meta.AttribData` [↵](#242-types-)

The corresponding builtin name is `meta_attrib_data`.

## 24.3. Precedences [↵](#24-language-builtins-)

The langauge knows about 2 precedences: `lowest_precedence` and `highest_precedence`.
These define both the highest and lowest precedence and are used to order any user defined precedences.

More info can be found [here](#15-precedence-).

## 24.4. Meta-functions [↵](#24-language-builtins-)

Meta builtins, unlike other builtins are not located within the `core` library, but in the `meta` library.
The represent special meta-functions which need additional compiler support to fully work.

> _Note_: Some of these may move to fully being standalone meta-functions once the required infrastrucure needed has been finalized, like the self-hosted compiler API.
>         This is indicated for each function

> _Todo_: Fix links


### 24.4.1. `line` [↵](#244-meta-functions-)

The corresponding builtin name is `line`.

This meta-variable returns the line on which the meta-attribute is invoked.

### 24.4.2. `column` [↵](#244-meta-functions-)

The corresponding builtin name is `column`.

This meta-variable returns the column on which the meta-attribute is invoked.

### 24.4.3. `group` [↵](#244-meta-functions-)

The corresponding builtin name is `group`.

The meta-variable returns the group name that the current package is in, or `None` if there in no group name.

### 24.4.4. `package` [↵](#244-meta-functions-)

The corresponding builtin name is `package`.

The meta-variable returns the current package name.

### 24.4.5. `library` [↵](#244-meta-functions-)

The corresponding builtin name is `library`.

The meta-variable returns the current library name.

### 24.4.6. `file` [↵](#244-meta-functions-)

The corersponding builtin name is `file`

The meta-function gets the file name.

> _Todo_: Add in docs: It can return the name, relative path to ..., and full path

### 24.4.7. `context` [↵](#244-meta-functions-)

The corresponding builtin name is `context`.

The meta-variable gives access to the implicit context.

### 24.4.8. `embed` [↵](#244-meta-functions-)

The corresponding builtin name is `embed`.

The meta-function allows external data to be directly included within code.

This is the only meta-function that has access to the file system, but solely relative to the project's root.

### 24.4.9. `asm` [↵](#244-meta-functions-)

The corresponding builtin name is `asm`

This meta-function allows for inline assembly directly within code

See its documentation for more info.

### 24.4.10. `tokenize` [↵](#244-meta-functions-)

The corresponding builtin name is `meta_tokenize`.

This meta-function is used to generate tokens from a given input.
It is also used to let the compiler know which tokenize meta-function to use for [meta-blocks](#meta-blocks-)

See [`#tokenize`](#1381-tokenize-partial_synth--synthesize-)

> _Note_: In the future, the sole purpose of this builtin is to indicate the tokenize function for meta-blocks and will have an independent implementation

### 24.4.11. `partiah_synth` [↵](#244-meta-functions-)

The corresponding builtin name is `partiah_synth`.

This meta-function is used to generate a fragment stream from a given input.
It is also used to let the compiler know which synthesize meta-function to use for [meta-blocks](#meta-blocks-)
 
See [`#synthesize`](#1381-tokenize-partial_synth--synthesize-)

### 24.4.11. `synthesize` [↵](#244-meta-functions-)

The corresponding builtin name is `meta_synthesize`.

This meta-function is used to generate an ast from a given input.
It is also used to let the compiler know which synthesize meta-function to use for [meta-blocks](#meta-blocks-)
 
See [`#synthesize`](#1381-tokenize-partial_synth--synthesize-)

> _Note_: In the future, the sole purpose of this builtin is to indicate the tokenize function for meta-blocks and will have an independent implementation

### 24.4.12. `pattern_parse` [↵](#244-meta-functions-)

The corresponding builtin name is `meta_pattern_parse`.

This meta-function is used to parse a given token input into a partial AST.

See [`#pattern_parse`](#1382-pattern_parse-)

> _Note_: In the future, this will be a standalone meta-function

### 24.4.13. `pattern_match` [↵](#244-meta-functions-)

The corresponding builtin name is `meta_pattern_match`.

This meta-function is used to match an input to one of the provided patterns or a wildcard, and execute that branch.

See [`#pattern_match`](#1383-pattern_match-)

> _Note_: In the future, this will be a standalone meta-function

### 24.4.14. `invoke_repr` [↵](#244-meta-functions-)

The corresponding builtin name is `invoke_repr`.

The meta-function allows the program to get the representation of a value at its invocation site.

See [`#invoke_rep`](#1384-invoke_repr-)

### 24.4.15. `meta.library` [↵](#244-meta-functions-)

The corresponding builtin name is `meta_library`.

Not to be confused with the core [`library`](#1385-library--package) meta-function.
This meta-variable is used to generate paths within a meta-attribute relative to the library the meta-function is defined in.

See [`#library`](#1385-library--package)

### 24.4.16. `meta.package` [↵](#244-meta-functions-)

The corresponding builtin name is `meta_package`.

Not to be confused with the core [`package`](#1385-library--package) meta-function.
This meta-variable is used to generate paths within a meta-attribute relative to the library the meta-function is defined in.

See [`#library`](#1385-library--package)

# 25. Main function [↵](#tables-of-contents)

The main function, unlike langauge builtins, is a special function that the user can themselves define.
It must appear in the root module of a binary, i.e. in `main.`.

The main function takes no arguments, and must return a type implementing the `Termination` trait.
This is meant to allow the main function to return any type that implements the trait.

Some examples of possible main functions are
```
fn main() {}
```
```
fn main() -> ! {

}
```
```
fn main() -> impl Termination {

}
```

> _Todo_: Make sure examples are valid, i.e. fix implementation

The main function may be an import from another library or a module in the current library by aliasing a function meeting the requirements as `main`.

```
mod foo {
    fn bar() {
    }
}

use :.foo.bar as main;

```

# 26. Preludes [↵](#tables-of-contents)

A prelude is a special file in a project which contains imports that are implicitly included with a project.
It is avaiable within every file that uses the library, and all public re-exports can be used as if they were explicilty imported.

If an explicit symbol with the same name as one in a prelude is imported, then the prelude symbol will be shadowed by it, and no errors will be generated

## 26.3. `core` prelude [↵](#26-preludes-)

The `core` prelude is included within every single library, and allow access to any core items required by the language to operator.

This does not however mean that every single item in the core library will be in the prelude.

## 26.2. `std` prelude [↵](#26-preludes-)

The `std` prelude is automatically included by the build tools and is generally always available.
If needed, the build system can be told to not include this prelude

## 26.3. Custom preludes [↵](#26-preludes-)

Libraries can add their own custom prelude, by having a `prelude.mn` file next to the `lib.mn` file.

This file is solely used for public re-exportimg imports, and cannot contain any other code, other than [`when` items](#717-when-items-) with other re-exports.

# 27. Panic [↵](#tables-of-contents)

To allow a program terminate whenever it reaches an error that is not recoverable, the language provides a mechanism for this, a "panic".

The compiler may also insert panics for illegal behaviors, for example when accessing an index out of range.

The user also has control over how a panic occurs, mainly:
- a user overwritable panic handler
- control on how FFI functions should handle a panic.

## 27.1. Panic handler [↵](#27-panic-)

A panic handler is a function with the `@panic_handler` attribute, and has the following signature:
```
@panic_handler
fn panic_handler(info: &PanicInfo) -> ! {
    ...
}
```

This allows the library providing the panic_handler to do whatever it wished to do wit hteh provided information.

### 27.1.1. Standard behavior [↵](#271-panic-handler-)

The `std` library provides a standard panic handler, which can do either of the following
- unwind: unwinds the stack, allowing more information about the panic to be provided
- abort: aborts the process, which may be recoverable

> _Note_: 'unwind' is not supported on all platforms

If the library of program is compiled without the standard library, a custom panic handler needs to be provided.

## 27.2. Unwinding [↵](#27-panic-)

Unwinding allows a panic to be recoverable, as any non-unwinding panic handlers will always be un-recoverable.
An unwinding panic handler however does not guarantee that the panic is recoverable, as it depends on its implementation.
Only the panic handler supplied by the standard library are guaranteed to be recoverable.

When a panic occurs, the unwind handler will "unwind" all stack frames, until it reaches a point where it can be recovered.
This also means that any object that implement `Drop` will have their `drop` functions called during the unwind.
This allows all the object to be cleaned up whenever a recoverable point is reached

> _Note_: Recovering from a panic should be avoided unless there is a good reason for it, for example, a VM which can restart processed on panic, like erlang's BEAM.

> _Note_: Compiling with unwind support will result in larger binaries

> _Todo_: Add links to parts in the std lib that can be used to handle it

### 27.2.1. Unwinding over FFI boundaries [↵](#272-unwinding-)

It is possible to unwind across FFI boundaries using an appropriate ABI.
While this can be useful, it does require that the FFI library uses a matching unwind implementation and the program using it.
Mismatched implementation may cause unexpected behavior.

> _Todo_: Add more details about unwinding with uncompatible APIs

### 27.2.2. Function unwinding linkage [↵](#272-unwinding-)

ABIs generally come in 2 flavors, a normal and an `-unwind` variant.
Generally both the `minoa` ABIs, with and without contexts, support unwinding, if enable when compiling those libraries.

> _Todo_: Add more info when the API/ABI has been figured out

# 28. Unsafety [↵](#tables-of-contents)

Unsafe operators are operators that could potentially violate any memory-safety guaranteed provided by Minoa.

The following features cannot be used outside of an explicitly unsafe context:
- Dereferencing a [pointer](#11112-pointer-types-)
- Reading from or writing to an [extern static](#7103-external-statics-)
- Accessing a field of a [union](#11119-union-types-), with the exception of writing
- Calling any `unsafe` function (including intrinsics or extern functions)
- Implementing an [`unsafe` trait](#7123-unsafe-traits-)
- Declaring an [`extern` block](#714-external--export-blocks-)