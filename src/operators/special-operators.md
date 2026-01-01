# Special operators
```
<special-ops> := <borrow-op>
               | <deref-op>
               | <assign-op>
               | <logical-ops>
               | <comparison-ops>
               | <try-ops>
               | <catch-op>
               | <contains-ops>
               | <constract-capture-op>
               | <in-place-op>
               | <end-relative-op>
```

Certain operators have additional behavior which cannot be implemented using an operator item alone, but require additional compiler support to work correctly.
This might additionally include a more complex syntax than just an individual operator.

These operators are known as _special operators_.

Additionally, some of these operator may also not be manually implemented and may only use the compiler provided implementation.
These operator will explicitly mention this when needed.

## Borrow [↵](#special-operators)
```
<borrow-op> := '&' [ 'raw' ] [ 'mut' ]
```

A borrow operator is a prefix operator which can be applied to [place expression].
The operator will take a reference (or a pointer) of the expression it is applied to.

In the case that the operator is applied to a value expression, this will result in a temporary value being created, and that temporary value being borrowed.
This limited the borrow to only be used within the expression containing it.

This borrow operator cannot be manually implemented.

It is however possible to provide a way of borrowing a type as a differnt type, this can be done via the [`Borrow`] and [`BorrowMut`] traits.

### Reference borrowing [↵](#borrow-)

When the resulting value is a [reference], the memory location it points to will be put in a borrowed state for the lifetime of the reference.
This ranges from the initial borrow, to the last use of the value.

If a shared borrow (`&`) is taken, the value stored in the memory location cannot be mutated, but only shared and read.
On the other hand, if a mutable borrow (`&mut`) is taken, the value may be mutated and read, but cannot be shared, i.e. the reference takes exclusive ownership of the borrowed value.

To be able to borrow a value, the value needs to be properly aligned to the alignment of the type of the value.
If this alignment cannot be guaranteed, it will result in an error.

> _Example_
> ```
> a := 1;
> 
> {
>     // multiple references can be taken when a shared borrow is used
>     a_ref0 := &a;
>     a_ref1 := &a;
> }
> 
> {
>     // only a single reference may be taken when a mutable borrow is used
>     a_mut := &mut a;
> 
>     // error: cannot borrow a value more than once if a mutable borrow is taken
>     // a_mut1 := &mut a;
>     // a := &a;
> 
>     // ensures the borrow lives until here, otherwise the above error examples would have been allowed
>     keep_ref_alive(a_mut);
> }
> ```

## Pointer borrowing [↵](#borrow-)

Alternatively, the operator may be used to explicitly get a [pointer] to the underlying value.
This is done using either `&raw` or `&raw mut`, taking a immutable and mutable pointer respectively.

> _Example_
> ```
> mut a := 1;
> 
> a_ptr := &raw a;
> 
> a_mut := &raw mut a;
> ```

Unlike a reference borrow, a pointer borrow lifts 3 restriction which are normally provided by it:

1) The pointer borrow may be used on values which are not properly aligned, but are still located on a byte boundary.
   This means that this operator can also not be used on any value which offset by a non multiple of 8 bits in a [bitfield].

   > _Example_
   > ```
   > packed struct Packed {
   >     a: u8,
   >     b: u16,
   > }
   > 
   > let packed = Packed{ a: 1, b: 2 };
   > 
   > // borrowing `b` using a reference borrow would result in a misaligned reference, so resulting in illegal behavior.
   > // so we can borrow the value using a pointer borrow
   > raw_b := &raw packed.b;
   > ```

2) A borrowed pointer may also point to any place which does not contain a value as determined by its type.
   Allowing it to point to uninitialized memory, or a [non-active union field].

   > _Example_
   > ```
   > struct Foo {
   >     val: bool
   > }
   > 
   > mut uninit := MaybeUninit[Foo].uninit();
   > 
   > // borrowing `&uninit.as_mut().b` using a reference borrow would result in reference to an uninitialized `bool`, so resulting in illegal behavior
   > // so we can borrow the value using a pointer borrow
   > raw_val := &raw uninit.as_mut().b;
   > ```

3) In addition, a pointer borrow may also create a pointer that would otherwise introduce incorrect aliasing assumptions.

   > _Example_
   > ```
   > mut a := 1;
   > 
   > a_ptr := &raw mut a;
   > 
   > // introduces an alias to `a`
   > a_alias := &raw mut a;
   > 
   > // The compiler cannot assume that these 2 expressions are independent to each other when trying to optimize this code, as assuming that 2 mutagble pointer `a_ptr` and `a_alias` are not-aliases would be incorrect
   > 
   > unsafe { a_ptr^ = 2; };
   > 
   > set_value(a_alias, 3);
   > 
   > // if the compiler could assume that `a_ptr` and `a_alias` would not be aliased, it could optimize this call to `println("2");`
   > println("\{^a_ptr}");
   > ```

Any of these would result in [illegal behavior] when used with a reference borrow.

A pointer borrow by itself is a safe operation and is therefore not required to be done in an `unsafe` context.

> _Note_: This does not result in the memory location being put in a borrowed state, meaning there is not guarantee that when using the resulting value, the value is not being mutated or read elsewhere.
>         When using an interpreted debug or hardened build, the interpreter will track this state and might produce a warning if this were to happen

## Dereferencing [↵](#special-operators)
```
<deref-op> := '^'
```

A dereference (`^`) operator is both a pre- and postfix operator which results in a [place expression].

The derefence operator comes in 3 variants.
The first 2 variants are the immutable and mutable derefernces, only allowing the value to be read, or both be read and mutated respectively.

> _Example_
> ```
> mut a := 42;
> 
> {
>     a_ref := &a;
> 
>     // immutable dereference
>     a_val := ^a_ref;
>     // or
>     a_val := a_ref^
> }
> 
> {
>     a_mut := &mut a;
> 
>     // mutable derefernce
>     a_val := ^a_mut;
>     // or
>     a_val := a_mut^
> }
> ```

The last variant, called a _move derefernce_, is a special variant allowing a value to be moved out of the the resulting place expression.
This will invalidate the memory location of the value.

Since the compiler needs to be able to ensure that the underlying value is valid, this variant can only be used in a location where the compiler can guarantee that the value will either be re-assigned, or be dropped.

> _Example_
> ```
> struct Foo {
>     val: MoveOnlyTy,
> }
> 
> mut foo := Foo { val: ... };
> foo_ptr := &raw mut foo;
> 
> // move dereference
> val := foo_ptr^.val
> ```

Dereferencing a [pointer] requires the operation to be done in an `unsafe` context.

The associated trait are the following:
- [`Deref`]: immutable dereference
- [`DerefMut`]: mutable dereference
- [`DerefMove`]: move dereference

## Assignment [↵](#special-operators)
```
<assign-op> := '='
```

The assignment operator is, as its name implies, an assign operator, which moves the value of its right-hand operand into the memory location its left-hand operand.

Before the actual assignment happens, the operator will first drop the current value of the assignee, unless it is an unitialized variable.
Only then, will it move the value of its right-hand operand into the left-hand operand.

This operator cannot be manually implemented.

### Destructuring assignment [↵](#assignment-)

Additionally, the assignment operator can also be used to destructure a complex value into a its fields.

In contrast to a [variable declaration], the left-hand operand may not be a pattern due to syntactic ambiguities.
Instead, a group of expressions designated to be [assignment expressions] are permitted on the left-hand operand.

When using an undersore (`_`), the resulting value is explicitly ignore, for example, this allows the user to discard the result of a [`@must_use`] function, which would otherwise not be allowed.

> _Implementation_: When the assignee does not represent a [place expression], the assignment will be desugared into a pattern match, followed by the actual assignment.
>                   The desugared pattern must be irrifutable.

## Logical AND & OR [↵](#special-operators)
```
<logical-ops> := '&&' | '||'
```

The logical AND and OR operators are lazy infix operators, meaning that they only evaluated their right-hand operand depending on the value of the right-hand operand.

These operator cannot be manually implemented.

Examples for both operators use the following common code
```
fn left(_ val: bool) -> bool {
   println("Left hand evaluated");
   val
}

fn right() -> bool {
   println("Right hand evaluated");
   true
}
```
### Logical AND [↵](#logical-and--or-)

The logical AND (`&&`) operator will first evaluated its left-hand parameter, and only if it results in `true`, will it evaluate its right-hand operand.

In addition, it also propagates any [bindings] created in its left-hand operand to the right-hand operand, allowing it to be used, as defined in the [branch condition] rules.

This operator has a precedence of `LogicalAnd`.


_Example_
```
if left(false) && right() {}
// will print only "Left hand evaluated", while

if left(true) && right() {}
// will print both "Left hand evaluated" and "Right hand evaluated"
```

_Example_
```
a: ?i32 = 3;

// `&&` will propagate the `val` binding to the right side
if let .Some(val) = a && val == 3 {

}
```

### Logical OR [↵](#logical-and--or-)

The logical OR (`||`) operator will first evaluated its left-hand parameter, and only if it results in `false`, will it evaluate its right-hand operand.

Unlike the `&&` operator, this operator also allows only the propagation of any common [bi] of any bindings created by its left-hand operand, in the right-hand operand, as defined in the [branch condition] rules.

This operator has a precedence of `LogicalOr`.

_Example_
```
if left(true) || right() {}
// will print only "Left hand evaluated", while

if left(false) && right() {}
// will print both "Left hand evaluated" and "Right hand evaluated"
```

_Example_
```
a: ?i32 = 4;
b: ?i32 = 3

// `||` will not propagate the `val` binding to the right operand
// error: `val` cannot be propagated from the left side of `||` to its right side
if let .Some(val) = a || val == 4 {}

// however, `||` will propagate common bindings outside of the expression
if (let .Some(val) = a || let .Some(val) = b) && val == 3 {

}
```

## Comparison [↵](#core-operators)
```
<comparison-ops> := <eq-op> | <iden-op> | <ord-op> | <total-ord-op>
<eq-op>          := '==' | '!='
<iden-op>        := '===' | '!=='
<ord-op>         := '<=>?'
                  | '<' | '<=' | '>' | '>='
                  | '<?' | '<=?' | '>?' | '>=?'
<total-ord-op>   := '<=>'
```

Comparison operators are infix operators, which can be use used to check for a relationship between 2 values.

Below is a table of each operator, their meaning, their associated trait and function, and their default implementation if not provided

operator | meaning                | trait      | method      | return type | default implementation
---------|------------------------|------------|-------------|-------------|-----------------------------------------------------
`==`     | equal                  | `Eq`       | `eq`        | `bool`      | n/a
`!=`     | not equal              | `Eq`       | `neq`       | `bool`      | `!(a == b)`
`===`    | identical              | `Iden`     | `iden`      | `bool`      | n/a
`!==`    | not identical          | `Iden`     | n/a         | `bool`      | `!(a === b)`†
`<=>`    | order comparison       | `Ord`      | `cmp`       | `?Ordering` | n/a
`<`      | less than              | `Ord`      | `lt`        | `bool`      | `{ ord := (a <=>? b); ord == .Lt }`
`<=`     | less or equal          | `Ord`      | `le`        | `bool`      | `{ ord := (a <=>? b); ord == .Lt \|\| ord == .Eq }`
`>`      | greater than           | `Ord`      | `gt`        | `bool`      | `{ ord := (a <=>? b); ord == .Gt }`
`>=`     | greater or equal       | `Ord`      | `ge`        | `bool`      | `{ ord := (a <=>? b); ord == .Gt \|\| ord == .Eq }`
`<=>!`   | total order comparison | `TotalOrd` | `total_cmp` | `Ordering`  | n/a
`<!`     | total less than        | `Ord`      | `total_lt`  | `bool`      | `{ ord := (a <=> b); ord == .Lt }`
`<=!`    | total less or equal    | `Ord`      | `total_le`  | `bool`      | `{ ord := (a <=> b); ord == .Lt \|\| ord == .Eq }`
`>!`     | total greater than     | `Ord`      | `total_gt`  | `bool`      | `{ ord := (a <=> b); ord == .Gt }`
`>=!`    | total greater or equal | `Ord`      | `total_ge`  | `bool`      | `{ ord := (a <=> b); ord == .Gt \|\| ord == .Eq }`

† always implemented as this

> _Note_: If no implementation for `Eq` exists, but one does exist for `Iden`, then `Eq` will be implemented in terms of `Iden`.
>         Similarly, if no implementation for `Ord` exists, but one does exist for `TotalOrd`, then `Ord` will be implemented in terms of `TotalOrd`.

All operators are defined as `by_ref`.

These operators have a precedence of `Compare`.

The operators have an associativity of `none`, meaning it is not possible to chain multiple comparisons after each other.

> _Example_
> ```
> a == b == c
> ```
> is not a valid statement, as it cannot be determined wether `a == b` or `b == c` takes precedence over the other call.
> 
> This means the comparisons require explicit parantheses:
> ````
> (a == b) == c
> // or
> a == (b == c)
> ```

> _Note_: `Iden` can generally be though of as doing a `memcmp` on the value, for certain type like smart pointer, this might first try to shortcut more complex checks as "pointing to the same address"

> _Note_: When applying comparison opertors on either a [references] or [pointers], these operator will apply on the underlying value, not on the value of the references or pointer.
>         To ensure the direct references or pointer are compared, either [`ref_cmp`] or [`ptr_cmp`] can be used.

### Guarantees [↵](#comparison-)

In addition to these just the operators, they also define a set of guarantees about a type based on the trait that is implemented.
These exists out of 2 pairs of related operators, each providing a common set of guarantees, each with slight differences:
- `Eq` and `Iden`:
  - Both have the following properties:
    - communitativity, i.e. `a == b` implies `b == a`
    - transitive, i.e. `a == b` and `b == c` implies `a == c`
    - relationally consistent, i.e. `a != b` result in the same as `!(a == b)`
 
  - `Eq` only provides a means to compare 2 values, but does not guarantee that:
    - multiple values can be equal each other, e.g. `0.0 == -0.0`
    - values are not always equal to themselves, e.g. `f32.NAN != f32.NAN`
  - `Iden` defines that only 1 single value exists which is identical to it, i.e. itself
  
- `Ord` and `TotalOrd`:
  - Both have the following properties
    - communativity, i.e. `a < b` implies `b > a`
    - transitive, i.e. `a < b` and `b < c` implies `a < c`
    - relationally consistent, i.e. `a < b` results in the same as `!(a >= b)`, and `a > b` is the same as `!(a <= b)`
  
  - `Ord` 
    - only provides a means to get a relative order to values, but does not guarantee that 2 values have an order relative to each other
    - has `Eq` as its supertrait
  - `TotalOrd`
    - guarnantees that all values are consistently ordered relative to each other, and only 1 value can adhere to `!(a < b || a > b)`
    - has `Iden` as its supertrait

When implementing the latter 2, they must also be relationally consistent to the their respective `Eq` or `Iden` trait, i.e.
`a == b` implies `!(a < b || b > a)`, but `a != b` implying `a < b || a > b` only holds for implementations of `Iden` and `TotalOrd`.

Not adhering to these guarantees is considered [illegal behavior], and the compiler may therefore rely on these relations.

> _Example_
> 
> Below are some examples of differences between `Eq` and `Iden`
> ```
> f32.NAN != f32.NAN;
> // but
> f32.NAN === f32.NAN;
> 
> -0.0 == 0.0;
> // but
> -0.0 !== 0.0;
> ```
> 
> and differentes between `TotalOrd` and `Iden`
> ```
> (f32.NAN <=> f32.NAN) == null;
> // but
> (f32.NAN <=>! f32.NAN) == .Eq;
> 
> (-0.0 <=> 0.0) == .Eq;
> // but
> (-0.0 <=> 0.0) == .Lt;
> ```

## Try [↵](#special-operators)
```
<try-ops> := '?' | '!'
```

A try operator is a postfix operator, which is a variant of the [try expression], depending on which exact operator is used.
These operators are used to affect the control flow when an erroneous value is produced.

These operators cannot be manually implemented.

### Propagating try [↵](#try-)

The propagating try operator (`?`) is the equivalent of `try`, meaning that if an erroneous value is encountered, it will re-[`throw`] the error.
This means that this variant will also trigger the evaluation of any relavent [`err defer`].

> _Example_
> ```
> fn foo() -> ?i32 { ... }
> 
> fn do_something() -> ?i32 {
>     a := foo()?;
>     // equivalent to
>     a := try foo();
> 
>     // ...
> }
> ```

The operator cannot be directly followed by a any expression which supports optional chaining, such as a [field access].
To ensure the `?` operator is called, it can be wrapped using parentheses.

> _Example_
> ```
> struct Foo {
>     val: i32
> }
> 
> foo: ?Foo = Foo{ val: 2 };
> 
> // this will result in optional chaining
> foo?.val;
> 
> // this can be gotten around in the following way, as the try operator has been separated from the `.`
> (foo?).val;
> ```

The associated trait is [`Try`].

### Unwrapping try [↵](#try-)

The unwrapping try operator is the equivalent to `try!`, meaning that if an erroneous value is encountered, it will panic.

> _Example_
> fn foo() -> ?i32 { ... }
> 
> fn do_something() -> i32 {
>     a := foo()!;
>     // equivalent to
>     a := try! foo();
> 
>     // ...
> }
> ```

The associated trait is [`TryUnwrap`].


## Catch [↵](#special-operators)
```
<catch-op> := '??'
```

The catch (`??`) operator is a lazy infix operator, which is a variant of the [`catch` expression].

This operator is used to affect the control flow when an erroneous value is produced.
Specifically, if the left-hand operand results in an erroneous value, the right-hand operand will be evaluated and its value will be returned.

The associated trait is [`Catch`].

The operator has a precedence of `Select`.

_Example_
```
a: ?i32 = null;

// the `??` has a higher precedence than `==`
assert(a ?? 2 == 2);
```

## Contains [↵](#core-operators)
```
<contains-ops> := 'in' | '!in'
```

The contains operators are infix operator, which can check wether or not a value is contained within a range or collection of values.
These are the only operators which use non-punctuation symbols.

`!in` is always implemented in terms of `!(lhs in rhs)` and cannot be manually implemented.

The associated trait is `Contains`.

These operators have a precedence of `Contrains`.

## Contract capture [↵](#special-operators)
```
<contract-capture-op> := '$'
```

The contract capture (`$`) operator is a postfix operator, which allows a `post` contract to capture the value of an expression as it was at the start of the function.

The operator is designed to work together with any type which implements the [`Clone`] trait.
It will evaluate the expression it is applied to and will then clone this value until the execturion of the `post` contract.

This is only allowed inside of a [`post` contract].
The operator cannot be manually implemented.

> _Example_
> ```
> fn increment(val: &mut i32)
>    post(val == val$ + 1)
> {
>    *val += 1;
> }
> ```

## In-place construction [↵](#special-operators)
```
<in-place-op> := '<-'
```

The in-place (`<-`) operator is an assignment operator.
Instead of just moving the right-hand value into the left-hand value, uses the right-hand operand to construct a value in the left-hand operand.

A use-case in which this operator could be useful, is if the resulting value would be too big to first construct on the stack, and to then move it into the left-hand expression.

The right-hand may be one of the following:
- [constructiong expression]
- an [initializer]
- any expression fullfilling the same rules as [`chain`], when taking in a type adhering to `is Fn(&mut MaybeUninit[Lhs], ..., Tn)`, where `Lhs` represents the type of the left-hand operand

The operator cannot be manually implemented.

> _Example_
> ```
> struct Foo {
>    val: i32
> 
>    init fn(_ val: i32) {
>       self.val = val;
>    }
> }
> 
> foo: Foo;
> 
> // using a constructing expression
> foo <- Foo{ val: 2 };
> 
> // using an initializer
> foo <- Foo(2);
> 
> // using a function which adheres to the expected signature
> 
> fn make_foo0(foo: &MaybeUninit[Foo]) { 
>    foo.as_mut().val = 2;
>    unsafe foo.init()
> }
> 
> fn make_foo1(foo: &mut MaybeUninit[Foo], _ val: i32) {
>    foo.as_mut().val = val;
>    unsafe foo.init()
> }
> 
> foo <- make_foo0();
> foo <- make_foo0(_);
> foo <- make_foo1(3);
> foo <- make_foo1(_, 4);
> ```

## End-relative index [↵](#special-operators)
```
<end-relative-op> := '^-'
```

The end-relative (`^-`) operator, is a prefix expression, which allows an offset to be calculated relative to the end of type supporting indexing.

This operator is only allowed within the index of an [index expression].

This operator cannot be manually implemented.
Instead, this operator relies on `Index`'s optional `end_rel?(offset: Idx)` method of the indexing expression containing it.
If this method is not present, using the operator will result in an error.

> _Example_
> ```
> foo := IndexableTy;
> 
> foo[^-4];
> // equivalent to
> foo[foo.len() - 4];
> ```


[`@must_use`]:              ../attributes.md "Todo: fix up link"
[`post` contract]:          ../contracts.md "Todo: fix up link"
[assignment expressions]:   ../expressions.md#assignee-expressions-
[place expression]:         ../expressions.md#place-expressions-
[`catch` expression]:       ../expressions/catch-expressions.md
[constructiong expression]: ../expressions/constructing-expressions.md
[field access]:             ../expressions/field-access-expressions.md
[bindings]:                 ../expressions/if-expressions.md#let-bindings-
[branch condition]:         ../expressions/if-expressions.md#branch-conditions-
[index expression]:         ../expressions/index-expressions.md
[`throw`]:                  ../expressions/throw-expressions.md
[try expression]:           ../expressions/try-expressions.md
[illegal behavior]:         ../illegal-behavior.md
[initializer]:              ../items/initializers.md
[`chain`]:                  ../operators.md
[`err defer`]:              ../statements/defer-statements.md#error-defer
[variable declaration]:     ../statements/variable-declarations.md
[bitfield]:                 ../type-system/types/composite-types/bitfield-types.md
[non-active union field]:   ../type-system/types/composite-types/union-types.md#union-field-access-
[reference]:                ../type-system/types/pointer-like-types/reference-types.md
[references]:               ../type-system/types/pointer-like-types/reference-types.md
[pointer]:                  ../type-system/types/pointer-like-types/pointer-types.md
[pointers]:                 ../type-system/types/pointer-like-types/pointer-types.md

[`Borrow`]:                 #borrow- "Todo: link to docs"
[`BorrowMut`]:              #borrow- "Todo: link to docs"
[`Deref`]:                  #dereferencing- "Todo: link to docs"
[`DerefMut`]:               #dereferencing- "Todo: link to docs"
[`DerefMove`]:              #dereferencing- "Todo: link to docs"
[`Try`]:                    #propagating-try- "Todo: link to docs"
[`TryUnwrap`]:              #unwrapping-try- "Todo: link to docs"
[`Clone`]:                  #contract-capture- "Todo: link to docs"
[`Catch`]:                  #catch- "Todo: link to docs"
[`Index`]:                  ../expressions/index-expressions.md "Todo: link to docs"
[`ref_cmp`]:                #comparison- "Todo: links to docs"
[`ptr_cmp`]:                #comparison- "Todo: links to docs"
