# Function types

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

Function types are however able to coerce into [function pointer types] that have a matching signature.
This can happen at the following sites:
- when a function is passed to place where a matching function type is expected
- when a function is returned in different arms of a control flow expression.

During coercion, all constant parameters will be collapsed before coercing, for more info can be found [here]()

> _Todo_: is 'collapsing' correct terminology? + add an actual description for it

> _Todo_: We somehow need to bake lifetimes propagation into this type



[function pointer types]: ./function-pointer-types.md