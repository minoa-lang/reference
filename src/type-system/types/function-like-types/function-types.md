# Function types

A function type is an anonymous 0-sized type, that is unique to every function.
It is compiler generated and cannot be manually defined.

A function type takes into account the name and the full signature (including parameter labels, and compile-time parameters) of the function.

Since each type exclusivly represents 1 function, there is no need for any indirection to be used to call it, nor does it need to store a function pointer.

When an error message is generated, the pseudo-type `fn name(param_label:type) -> type` will be used.


Since each function type is unique, mixing function type without casting it to something like a pointer to a raw function type, will cause an error:
```
fn foo(val:i32);
fn bar(val:i32);
x := &foo;
x = &bar; // error: type mismatch
```

This also happens when generic types or values differ:
```
fn foo(const T: type);
x := &foo(i32);
x = &foo(u32); // error: type mismatch

fn bar(const V: i32);
x := &bar(0);
x = &bar(1); // error: type mismatch
```
this happends because all generic parameters are [curried] into the function definition.

All function implement the following interfaces:
> _Todo_

## Implicit coercion [↵](#function-types)

Function tyes can however implcitly coerce to a pointer to a [raw function type] in the following locations:
- when a function is passed to a place where the matching the above type with signature is expected
  ```
  fn foo(u32) -> bool;
  fn bar(f: ^fn(u32) -> bool);
  
  // Implicit conversion
  bar(&foo);
  ```
- when a function is returned in different arms of a control-flow expression.
  ```
  fn foo(u32) -> bool;
  fn bar(u32) -> bool;
  
  // `f` is of type `^fn(u32) -> bool`
  let f = if a == b {
      &foo
  } else {
      &bar
  }
  ```
- when being assigned to a variable with a matching signature
  ```
  fn foo(u32) -> bool;
  
  // Implicit conversion
  let x: ^fn(u32) -> bool = &foo;
  ```




[raw function type]: ./raw-function-types.md
[curried]:           #function-types "Todo: Link to function currying"