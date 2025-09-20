# Closure types

A closure type is an anonymous type which is unqieu to every closure.
It is compiler generated and cannot be manually defined.

If a closure does not capture any values, they will be 0-sized and can be coerced into a pointer to a [raw function type].
Implicit coercion is allowed in the same [locations] as a function type.

Otherwise the closure is large enough to fit all values passed to it.

## Capture modes [↵](#closure-types)

Capture modes define how a closure type will store values used within the generated closure type.
These modes can either be decided implicitly based on the use of each value, or explicilty by providing the way to capture them within the closure's capture list.

Values in this context means any place expression that is used within the closure.

The following modes are supported:
1) immutable/shared borrow: the value is captured via a [shared referece]
2) unique borrow: similer to an immutable borrow, but must be unique as defined [below](#unique-immutable-borrows-in-captures-)
3) mutable borrow: the value is is captured as a [mutable reference]
4) move, also known as `by value`: the value is captured by moving it into the closure

If a value is not explicitly captured, the capture mode will be decided based on the above order and will pick the first option which is a valid mode.
The implicit capture mode is not affected by any code surrounding the location of the closure, i.e. it's decided solely on the closure's body.

### Copy values [↵](#capture-modes-)

Any value which implements `Copy` will always be implicitly captured as an immutable borrow.

### Async captures [↵](#capture-modes-)

Async closures will always capture all values present in the closure body, regardless of whether the value is used or not.

## Capture precision [↵](#closure-types)

To capture values more precisely, meaning that the compiler will try and capture with the most specific capture mode, the capture path is used.
A capture path is a sequence of specific sub-expressions on a value within the code, of which it can propagate a capture mode to.
These sub-expressions are the following:
- [field access]
- [tuple index]
- [dereference]
- [array or slice index]

The capture can then use this path to do a borrow of the deepest element within the path.

> _Example_:
> ```
> struct Foo {
>     bar: (i32, i64),
> }
> foo := Foo{ f1: (1, 2) };
> 
> c := fn {
>     x := foo.bar.1; // Captures `foo.bar.1`
> }
> ```
> In this code, the first the local variable is captured, followed by the `bar` field, and finally the tuple index `1`.
> This result in the closure capturing `foo.bar.1` by an immutable borrow, which also means it borrows both `foo` and `foo.bar`

### Shared prefix [↵](#capture-precision-)

In case a capture paths and one of its ancestors are both captured, the ancestor path is captured with the highest capture mode among the 2 captures, as defined by the [capture modes] above.

> _Example_
> ```
> a := "A"s;
> b := (a, "B"s);
> mut c := (b, "C"s);
> 
> let closure = fn {
>     foo(&c); // captures `c` immutably
>     modify(&mut c.0)/ // captures `b` mutably
>     move_value(c.0.0); // capture `c` by value
> }
> ```
> This will result in `c` being captured by value, which is decided in the following way:
> - `c.0.0` is captured by value
> - `c.0` is captured mutably, but since it's the ancestor of `c.0.0` which has a higher capture mode, it will be captured by value
> - `c` is captured immutably, but since it's the ancestor of `c.0` which has a higher capture mode, it will be captured by value

### Rightmost shared reference truncation [↵](#capture-precision-)

The capture path is trucated at the rightmost deference in the capture paths if the dereference is applied to a shared reference.

This trunctaon is allowed because fields that are read through a shared reference will always be read via a shared reference or a copy.
This helps reduce the size of the capture when the extra precision does not yield any benefit from a borrow checking perspective.

The reason for picking the rightmost dereference, is to help avoid a shorter lifetime than is necessary.

> _Example_: Consider the following:
> ```
> struct Int(i32);
> struct B(&i32);
> 
> struct MyStruct {
>     a: &Int
>     b: B
> }
> 
> fn foo(m: &MyStruct) -> impl FnMut() {
>     c := fn{ drop(&m.a.0) };
>     c
> }
> ```
> If this were to capture `m`, it's lifetime would be depedendent on the reference passed to `foo`.
> If we instead capture `m^.a^` using an immutable borrow, we can have the same lifetime as the inner `a`.

> _Todo_: Since this example is based on rust, the syntax may not be right + we aren't necessarily going to have the same lifetime system as rust

### Wildcard pattern bindings [↵](#capture-precision-)

Closures only capture data that needs to be read.
Binding a value with a [wildcard pattern] does not count as a read, and thus won't be captured.

The following closure does not capture `x`
```
x := "hello"s;
c := fn {
    // Does not capture, as we are binding to a wildcard.
    _ := x;
}
c();
```

This also includes any matches that only contain a wildcard pattern.

```
x := "hello"s;
c := fn {
    // Does not capture, as the value of x is only bound to a wildcard
    match x {
        _ => (),
    }
}
```

This also includes the destructuring of tuples, structs, and enums.
Similarly, fields matched using a [rest pattern] or a [struct rest pattern] are also not considered as being read, and will also not be captured.

```
x := ("a"s, "b"s);
c := fn {
    // Captures `x.0` by value
    let (first, ..) = x;
};

// `x.1` can still be used here
do_something(x.1);
c();
```

However, partially capturing array or slices is not allowed.
The entire slice or array is always captured, even if used with a wildcard pattern matching, indexing, or sub-slicing.

```
x := [ 0, 1 ];
c := fn {
    // Captures all of `x` by value
    let [first, _] = x;
};
c();

do_something(x[1]); // error: borrow of moved value `x`
```

Any values that are matched using a wildcard must always be initialized.

```
let x: i32;
c := fn {
    // error: `x` must be initialized
    let _ = x;
}
```

### Capturing references in move contexts [↵](#capture-precision-)

Because it is nt allowed to move fields out of a refeerence, `move` closures will only capture the prefix of a capture path that runs up to, but not including, the first derefence of a reference.
The reference iteself will be moved into the closure:
```
struct T(String, String);

mut t := T("foo"s, "bar"s);
t_mut_ref := &mut t;
c := fn { [move] =>
    t_mut_ref.0.push(str: "123"); // captures `t_mut_ref` by value
}
c();
```

### Pointer dereference [↵](#capture-precision-)

Because it is `unsafe` to dereference a pointer, closures will only capture the prefix of a expression path that runs up to, but not including, the first dereference of a pointer.
```
struct T(String, String);

t := T("Foo":s, "bar":s);
t_ptr := &t as ^T;

c := unsafe fn {
    do_something(t_ptr^.0); // captures `t_ptr` immutably
};
```

### Union fields [↵](#capture-precision-)

Because it is `unsafe` to access a union field, closures will only capture the prefix of a capture path that runs up to the union itself.
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

### References to unaligned structures [↵](#capture-precision-)

Because it is [illegal behavior] to create a reference to  unaligned fields in a structure, closures will only capture the prefix of the capture path that runs up to, but not including, the first field access into a ['packed`] structure or a [bitfield].
This inclused all fields, even those that are aligned, to protect against compatibility concerns should any of the fields in the structure of bitfield change in the future.

```
@repr(packed)
struct T(i32, i32);

t := T(1, 2);
c := fn {
    a := t.0; // captures `t` immutably
}
// Copies out of `t` are still valid
a, b := t.0, t.1;
c();
```
or
```
bitfield T {
    a: i32,
    b: i32,
}

t := T{ a: 1, b: 2 };
c := fn {
    a := t.a; // captures `t` immutably
}

// Copes out of `t` are still valid
a, b := t.a, t.b;
c();
```

Similarly, taking the address of an unaligned field also captures the entire struct or bitfield.
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
or
```
bitfield T {
    a: String,
    b: String,
}

mut t := T(String.new(), String.new());
c := fn {
    a ;= #std.ptr.addr_of(t.b); // captures `t` immutably
}
a := t.0; // error: cannot move out of `t.a` because it is borrowed
c();
```

> _Todo_: is the path of `addr_of` correct?

But the above works if the struct is not packed, since it can capture  the field precisely.
```
struct T(String, String);

mut t := T(String.new(), String.new());
c := fn {
    a ;= std.ptr.#addr_of(t.1); // captures `t.1` immutably
}
// The move is still allowed here
a := t.0;
c();
```

### `DerefPure` implementations [↵](#capture-precision-)

Implementers of the `DerefPure` are treated differently compared to other types, as this trait implies additional behavior.
The trait allows for the more precise capturing of values.

> _Note_: In both subsections, the type `Pure` represents a type implementing the above trait, while `Impure` does not

#### Within a non-`move` closure

Within a non-`move` closure, the contents of the type are not moved into the closure body, but instead the contents of the type are precisely captured

```
struct S(String);
x := Pure(S(String()));
c := fn {
    let x = &x^.0; // captures `x^.0` immutably
};
c();
```
This is in contrast with the following
```
struct S(String);
x := Impure(S(String()));
c := fn {
    let x = &x^.0; // captures `x` immutably
}
```

#### Within a `move` closure

In this case, it acts similarly to how a non-`DeferPure`, where it will capture the type entirely.
```
struct S(String);
x := Pure(S(String()));
c := fn {
    let x = &x^.0; // captures `x` immutably
}
```

## Unique immutable borrows in captures [↵](#closure-types)

Captures can occur by a special kind of borrow called a _unique immutable borrow_, which cannot be used anywhere else in the language and cannot be written out explicitly.
It occurs when modifying the reference of a mutable reference, as in the following example: 
```
mut b := false;
x := &mut b;
mut c := fn {
    // An immtable and mutable borrow of `x`
    a := &x;
    *x = true; // `x` captured by unique immutable borrow
}
// The following line is an error, as `x` is uniquely borrowed:
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

## Call traits and coercions [↵](#closure-types)

Closure types implement `FnOnce`, indicating that they can be called once by consuming ownership of the closure.
Additionally, some closures implement more specific call traits:
- A closure which does not move out of any captured variables implements `FnMut`, indicating that it can be called by mutable references
- A closure which does not mutate or move out of any captured variables implements `Fn`, indicating that it can be called by shared reference.

> _Note_: `move` closures may still implement `Fn` and `FnMut`, even though they capture variables by move.
>         This is because the traits implemented by a closure type are determined by what the closure does with captured values, not how it captures them.

Non-capturing closures are closures that don't capture anything from their environment.
These can be coerced to function pionters (e.g. `fn()`) with the matching signature

> _Todo_: Using rust-like trait names now, is this correct?

## Async closure traits

> _Todo_: Figure out API first

## Generator closure traits
 
> _Todo_: Figure out API first

## Other traits

All closures types are `Sized`.
Additionally, they implement the following traits, if all captured values support them:
- `Clone`
- `Copy`


`Clone` and `Copy` are implemented as if they were derived, with the order of cloning being unspecified.

When closures capture references, some additional rules apply:
- `Clone` and `Copy` are only implemented if the closure does not capture any uniquely or mutably captured.

> _Todo_: Figure out additional APIs first

#### Drop order[↵](#closure-types)

If a closure captures a field of an composite type like a struct, tuple, or enum by value, the field's lifetime would not be tied to the closure.
As a result, it is possible for disjoint fields of an composite type to be dropped at different times.
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



[capture modes]:          #capture-modes-
[raw function type]:      ./raw-function-types.md
[bitfield]:               ../composite-types/bitfield-types.md
[locations]:              ./function-types.md#implicit-coercion-
[raw function type]:      ./raw-function-types.md
[shared referece]:        ../pointer-like-types/reference-types.md#shared-reference-
[mutable reference]:      ../pointer-like-types/reference-types.md#mutable-reference-
['packed`]:               ../../type-layout/layout-representation.md#packed
[field access]:           ../../../expressions/field-access-expressions.md
[tuple index]:            ../../../expressions/tuple-index-expressions.md
[array or slice index]:   ../../../expressions/index-expressions.md
[illegal behavior]:       ../../../illegal-behavior.md#incorrect-pointer-alignment-
[dereference]:            ../../../operators/special-operators.md#derefence-operator-
[rest pattern]:           ../../../patterns/rest-patterns.md
[struct rest pattern]:    ../../../patterns/struct-patterns.md#struct-rest-pattern-
[wildcard pattern]:       ../../../patterns/wildcard-patterns.md  