### Closure types

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

#### Capture modes [↵](#closure-types)

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

#### Capture precision [↵](#closure-types)

A capture path is a sequence starting with a variable from the surrounding environment, followed by zero or more place projectecion taht were applied to the variable.
A place projection is any of the following:
- [field access](../../expressions/field-access-expressions.md)
- [tuple index](../../expressions/tuple-index-expressions.md)
- [dereference](../../operators/special-operators.md#derefence-operator-)
- [array or slice index](../../expressions/index-expressions.md)

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

Closures only capture data that needs to be read. Binding a value with a [wildcard pattern](../../patterns/wildcard-patterns.md) does not count as a read, and thus won't be captured.
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

Because it is [illegal behavior](../../illegal-behavior.md#incorrect-pointer-alignment-) to create a reference to  unaligned fields in a structure, closures will only capture the prefix of the capture path that runs up to, but not inscluding, the first field access into a `packed` structure or a bitfield.
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

#### Unique immutable borrows in captures[↵](#closure-types)

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

#### Call traits and coercions[↵](#closure-types)

Closure types implement `FnOnce`, indicating that they can be called once by consuming ownership of the closure.
Additionally, some closures implement more specific call traits:
- A closure which does not move out of any captured variables implements `FnMut`, indicating that it can be called by mutable references
- A closure which does not mutate or move out of any captured variables implements `Fn`, idnicating that it can be called by shared reference.

> _Note_: `move` closures may still implement `Fn` and `FnMut`, even though they capture variables by move.
>         This is because the traits implemented by a closure type are determined by what the closure does with captured values, not how it captures them.

Non-capturing closures are closures that don't capture anything from their environment.
These can be coerced to function pionters (e.g. `fn()`) with the matching signature

> _Todo_: Using rust-like trait names now, is this correct?

#### Drop order[↵](#closure-types)

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
