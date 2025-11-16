# Struct expressions
```
<struct-expr>      := <struct-expr-path> '{' [ <struct-elems> ] '}'
<struct-expr-path> := <path> | '.'
```

A struct expression allows for the creation of a value with a [struct] or [union] type.

When creating a struct, all fields must be mentioned (with the exception defined below), but for the creation of just 1.

The struct expression can either have:
- an explicit name defining the type of the expression
- an inferred `.`, which will deduce the correct type from the surrounding context

## Struct elements [↵](#struct-expressions)
```
<struct-elems> := <struct-elem> { ',' <struct-elem> }* [ ',' <struct-functional-update> ] [ ',' [ <struct-completion> ] ]
                | <struct-completion>
<struct-elem>  := <ext-name> ':' <expr>
                | <functional-struct-update>
                | <field-assign-shorthand>
                | <field-init-shorthand>
```

Each element consists out of a name and an expression (unless one of the other special syntaxes).

The expression will then be assigned to a field with that name.
Each element will assign a value to exactly 1 field.

> _Example_
> ```
> Foo {
>     a:  i32,
>     3d: bool,
> }
> 
> // assign values to the respective fields
> foo := Foo {
>     a: 1,
>     3d: true,
> }
> ```

## Functional update [↵](#struct-expressions)
```
<functional-struct-update> := '..' <expr>
```

The functional update syntax allows any unassigned values to be taken from one of the followin expressions:
- a value with the same type as the structure being initialized
- a value with the type of a [use field]

These values will then be used to assign all unitialized corresponding values in the struct, or its corresponding use fields, repectively.
This does require that all field of the type, even unused fields, are available within the current scope.

These field will be moved or copied (if the field's type implements `Copy`) into the structure.
The compiler can also be told to close these fields by explicitly calling `T.(Clone.clone)()` on the provided expression.
The compiler can understand this and will not copy the entire expression, but only the fields that are missing in the struct expression.

These values will overwrite any default field values with the values from the provided type.

> _Note_: Only fields that are not explicitly assigned will be used in the provided value, meaning that any unused variables may be borrowed during this.

> _Example_
> ```
> Foo {
>     a: i32,
>     b: i32,
>     c: i32
> }
> 
> foo := Foo {
>     a: 1,
>     b: 2,
>     c: 3,
> };
> 
> foo2 := Foo {
>     a: 4,
>     // assigns `b` and `c` to `2` and `3` respectively
>     ..foo,
> };
> 
> 
> Bar {
>     x: f32,
>     y: f32,
>     use Foo,
> }
> 
> bar := {
>     x: 10.3,
>     y: 20.4,
>     a: 1,
>     b: 2,
>     c: 3,
> };
> 
> bar2 := {
>     x: 42.0,
>     // assigns `a`, `b`, and `c`
>     ..foo2,
>     // assigns `y`
>     ..bar,
> };
> ```

## Struct completion [↵](#struct-expressions)
```
<struct-completion> := '..'
```

A struct completion is similar to a functional update syntax without an explicit value.
Struct completion must be the last value in a struct expression.

The struct completion is equivalent to passing a `..T.(Default.default)()` value to the struct expression, and will fill an unspecified fields with a default value.

A sole '..' inside of the struct expression will therefore be identical to assigning `T.(Default.default)()` directly to a value.
This can be useful when wanting to set a type to its default value, when passing it through an [in-place operator].

> _Note_: A struct completion may **not** be followed by a `,`

> _Example_
> ```
> Foo {
>     a: i32,
>     b: i32,
>     c: i32
> 
>     impl as Default {
>         fn default -> Self {
>             Self { a: 4, b: 5, c: 6 }
>         }
>     }
> }
> 
> 
> foo := Foo {
>     a: 1,
>     // assigns `b` and `c` to `5` and `6` respectively
>     ..
> };
> 
> // defaults all fields
> foo2 := Foo { .. };
> // is equivalent to
> foo2 := Foo.default();
> ```

## Field assign shorthand [↵](#struct-expressions)
```
<field-assign-shorthand> := <name>
```

Whenever a variable with the same name as a field is assigned to that field, the argument may be simply defined as the name of the variable.
This allows for the writing of `fieldname` to initialize the field which would otherwise have been required to be written as `fieldname: fieldname`.
This does require only the name to be provided, and it may not be used as part of an expression.

This shorthand allows for a more compact syntax and less duplication.

> _Example_
> ```
> Foo {
>     a: i32,
>     b: i32,
> }
> 
> a := 2;
> b := 4;
> 
> foo := Foo {
>     // identical name to field, so we can leave the field out
>     a,
>     // identical name to a field, but we are using the value in an expression, requiring an explicit field name to be provided.
>     b: b + 1
> }
> ```

## Field initialization shorthand [↵](#struct-expressions)
```
<field-init-shorthand> := <ext-name> '{' [ <struct-elems> ] '}'
                        | <ext-name> <fn-args>
```

Whenever a field would be initialized using either a struct expression, or via a function-like call to an initializer, the field name may be directly succeeded with the syntax of the expression

> _Example_
> ```
> struct Bar {
>     a: i32
> }
> 
> struct Baz {
>     a: i32,
> 
>     init fn(val: i32) {
>         Self { a: val }
>     }
> }
> 
> struct Foo {
>     bar: Bar,
>     baz: Baz,
> }
> 
> foo := Foo {
>     // struct-like field init shorhand
>     bar{ a: 0 },
>     // init-like field init shorhand
>     baz(3),
> }
> ```

## Default fields [↵](#struct-expressions)

Fields with a default value may be left out within the struct expressions, and will be assigned their assigned default value.
Providing this field within the struct will override the default value of the field in the resulting expression.

> _Example_
> ```
> struct Foo {
>     a: i32,
>     b: i32,
>     c: i32 = 3;
> }
> 
> // `a` will be assigned a value of `3`
> foo := Foo { a: 0, b: 1 };
> ```

## Init properties [↵](#struct-expressions)

When a type declares an [init property], they may be directly used to assign the value of a field, and will then use the init property's setter to assign the value.
This comes with the restrictions declared in the [init property].

> _Example_
> ```
> struct Foo {
>     a: i32,
>     b: i32,
> 
>     init prop c { set(val) => self.b = val; }
> }
> 
> foo := Foo { 
>     a: 1,
>     // initialized `b`
>     c: 2,
> };
> 
> struct Bar {
>     a: i32,
>     b: i32,
> 
>     init prop c {
>         set(val) {
>             self.a = val;
>             self.b = val;
>         }
>     }
> }
> 
> bar := Bar {
>     // Initializes both `a` and `b`
>     c: 2,
> }
> ```


[init property]:     ../../items/properties.md#init-property-
[struct]:            ../../type-system/types/composite-types/struct-types.md
[use field]:         ../../type-system/types/composite-types/struct-types.md#use-fields-
[union]:             ../../type-system/types/composite-types/union-types.md
[in-place operator]: ../../operators/special-operators.md#in-place-operator-