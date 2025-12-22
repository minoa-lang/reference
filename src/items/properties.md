# Properties
```
<prop-item> := { <attribute> }* [ <vis> ] [ 'unsafe' ] [ 'const' [ '!' ] ] [ 'init' ] 'prop' <ext-name> ':' <type> <prop-body>
<prop-body> := <prop-accessors>
             | <prop-get-only>
             | <prop-direct-bind>
```

A property is a field-like value that is associated with a set of expressions that handle the underlyihng value changes.

A property are required to have at least 1 accessor.

Properties cannot exists by themselves, but must always be associated with a type.

Properties may be called using 2 kinds of expressions
- [field expressions]
- [method call expressions], either as `expr.name()` for getters, and `expr.name(value:)` for setters

> _Warning_: The use of properties might result in slower code, depending on the complexity of the property.
>            Direct bind property do not have any overhead.

> _Tooling_: It is recommended that tooling indicates to the user that the access is a property access and not a field access.

> _Todo_: A styleguide might define a different casing to clearly distinguish between fields and properties (except for shadowing and field properties).

> _Todo_: Once the effect system has been figured out, specify that the implementaions of properties might be required to be effect-free

## Accessors [↵](#properties)
```
<prop-accessors>     := '{' { ? <prop-accessor>, sub-elements need to be unique ? }* '}'
<prop-accessor>      := <prop-setter>
                      | <prop-getter>
                      | <prop-init>
                      | <prop-observer>
<prop-accessor-body> := '=>' <expr> ';'
                      | <block>
```

Accessors are used to either get, set, or observe the value.

Accessors may have their own [visibility], allowing them to have a more limited visibility.
If no visibility is provided, or the provided visibility would be greater than that of the actual property, the accessor will get the same visibility as the property.

Additionally, accessors may individually be declared as `const` or `const!`

Since properties can be defined inside of a [composite type]'s body, and the fact that `prop` is a weak keyword, it is possible to have a field which has the name `prop`.
An actual property is defined by `prop`, followed by a name.

> _Example_
> ```
> struct Foo {
>     // field named 'prop'
>     prop: i32
> 
>     // actual property
>     prop name: i32
> 
> }
> ```

### Getters [↵](#accessors-)
```
<prop-getter>       := [ <vis> ] [ 'const' [ '!' ] ] [ 'mut' | 'ref' ] 'get' <accessor-body>
<field-prop-getter> := [ <vis> ] [ 'const' [ '!' ] ] [ 'mut' | 'ref' ] 'get' ( <accessor-body> | ';' )
```
A getter is used to retrieve a value from the property.

A getter may also define how it will access `self`

There are 3 kinds of getters, each differing in how they default get access to `self` and what the type is that they return

getter    | kind     |`self` access | return type
----------|----------|-----------------------|-------------
`get`     | value    | `&self`               | `T`
`ref get` | refernce | `&self`               | `&T`
`mut get` | mutable  | `&mut self`           | `&mut T`

Both `ref` and `mut` getter will borrow the value containing the property as long as the property is borrowed.

Getters for field properties do not need to contain an body.

When calling a getter as a function, the exact getter will be resolved by the borrow applied to the method call.

> _Example_
> ```
> struct Foo {
>     inner: i32,
> 
>     property value {
>         get => inner;
>         ref get => &inner;
>         mut get => &mut inner;
>     }
> }
> ```

### Setters [↵](#accessors-)
```
<prop-setter>       := [ <vis> ] [ 'const' [ '!' ] ] 'set' [ '(' <name> [ ',' <name> ] ')' ] <accessor-body>
<field-prop-setter> := [ <vis> ] [ 'const' [ '!' ] ] 'set' [ '(' <name> [ ',' <name> ] ')' ] ( <accessor-body> | ';' )
```

A setter is used to modify the value of a property, requiring the value the property is part of to be mutable.

Setter can provide the name of the new value after the `set`.
And as mentioned above, setter have access to an implicit `&mut self`.

In cases that an observer is used in tandem with a setter, the setter may also provide a second value, allowed for the old value to be moved within this value.
If no `did_set` observer is present, this assignment will be optimized out.

Setters for field properties do not need to contain an body.

> _Example_
> ```
> struct Foo {
>     inner: i32
> 
>     property value {
>         set(value) => self.inner = value;
>     }
> }
> ```

#### Setter shorthand

Setters support a shorthand, allowing the parameter for the new value to be left out.
In this case, the implicit `$0` value can be used.

If a value should be moved out for a `did_set` observer, the implicit `$1` value can be used.

> _Example_ 
> ```
> struct Foo {
>     inner: i32
> 
>     prop value {
>         set => self.inner = $0;
>     }
> }
> ```

### Observers [↵](#accessors-)
```
<prop-observer>     := <prop-observe-pre>
                     | <prop-observe-post>
<prop-observe-pre>  := [ 'const' [ '!' ] ] 'will_set' [ '(' <name> ',' <name> ')' ] <accessor-body>
<prop-observe-post> := [ 'const' [ '!' ] ] `did_set` [ '(' <name> ',' <name> ')' ] <accessor-body>
```

Observer allow code to act upon the changing of a value, both before and after the value has been set.
This does however incur additional cost, as it needs to take a copy of the old and new value to be able to correctly pass them to the observer.

Observers are only allowed when the property contains a setter.

If the setter manually moves the old value into the temporary value for the `did_set` observer, the [destructor] will be delayed until after the assignment.

> _Example_
> ```
> struct Foo {
>     inner: i32,
> 
>     prop value {
>         set(value) => self.inner = value;
>         will_set(old, new) => print("Will change the value from \{old} to \{new}");
>         did_set(old, new) => print("Did changed the value from \{old} to \{new}");
>     }
> }
> ```

#### Observer shorthand [↵](#observers-)

Like setters, observer also support a shorthand allow both values to be left out.
In this case, the implicit `$0` value represents the old value, and `$1` the new value.

> _Example_
> ```
> struct Foo {
>     inner: i32,
> 
>     prop value {
>         set(value) => self.inner = value;
>         will_set => print("Will change the value from \{$0} to \{$1}");
>         did_set => print("Did changed the value from \{$0} to \{$1}");
>     }
> }
> ```

## Get-only properties [↵](#properties)
```
<prop-get-only> := '=>' <expr> ';'
```

Get-only properties, also known as read-only properties, allow for a property of which only the value can be retrieved from.
In this case, some syntactic sugar is provided for get-only property.

The expression has access to a `&self`, as it is a value getter.

> _Example_
> ```
> struct Foo {
>     prop value: i32 => 42;
> }
> ```

## Constant property [↵](#properties)

A constant property, not to be confused with a [constant], is similar to declaring a function as const.
Meaning that it allows the property to be used in a compile-time context.

In addition, if the property or any of its accessors is defined as `const!`, they will only be aviable at compile-time, and not at runtime.

## Init property [↵](#properties)

Init properties are special properties which can be used withing a [struct expression], instead of any corresponding fields.

Init properties come with the following restrictions:
- they must have a setter
- they must assign a value to each used variable in all codepaths
- they may only call functionality which is either:
  - not dependent on any other fields within the struct
  - is only dependent on fields that are already assigned within the property before their use

Field and direct-bind properties are automatically init properties.

> _Example_: Direct-bind init properties
> ```
> struct Foo {
>     x: f32,
>     y: f32,
> 
>     prop z: f32 := y;
> }
> 
> // `z` initializes `y`
> f := Foo { x: 1.0, z: 2.0 };
> ```

> _Example_: Init property not dependent on other fields
> ```
> struct Foo {
>     x: f32,
>     y: f32,
> 
>     init prop z: f32 {
>         set(value) => self.y = value;
>     }
> }
> 
> // `z` initializes `y`
> f := Foo { x: 1.0, z: 2.0 };
> ```

> _Example_: Init property depends on other field
> ```
> struct Foo {
>     x: f32,
>     y: f32,
>     z: f32,
> 
>     init prop yz {
>         set(value) {
>             // error: using possibly uninitialized value
>             // self.z = self.y + value;
> 
>             self.y = value;
> 
>             // dependent on `y`, but `y` is always assigned beforehand
>             self.z = self.y + value;
>         }
>     }
> }
> 
> // `y_yz` initializer `y` and `z`
> f := Foo { x: 1.0, yz: 2.0 }
> ```

## Direct-bind properties [↵](#properties)
```
<prop-direct-bind> := [ <prop-direct-bind-accessors> ] ':=' <path> ';'
<prop-direct-bind-accessors> := '{' { ? <prop-direct-bind-accessors>, sub-elements need to be unique ? }* '}'
<prop-direct-bind-accessor>  := [ <vis> ] ( 'set' | ( [ 'mut' | 'ref' ] 'get' ) )
```

A direct bind property is a property that directly maps to a field or another property within the type.
This includes fields or properties nested with another field.


The 2 main uses of this are:
- to give an alias to a field
- to restrict (partial) access to a field

Direct-bind properties do not allow for observers to be specified.

> _Example_
> ```
> struct Foo {
>     x: i32,
> 
>     prop r: i32 := x;
>     prop u: i32 { get } := x;
> }
> ```

_Example_: Nested bind
```
struct Foo {
    x: i32
}

struct Bar {
    foo: Foo

    prop y: i32 := foo.x
}

bar := Bar{ foo: Foo{ x: 3 } };

// Retrieves the value of `bar.foo.x`
bar.y
```

## Shadowing fields [↵](#properties)

A property is allowed to shadow a field that already exists within a [composite type].
When this is done, only the property that shadows the field can access the original field.

> _Example_
> ```
> struct Foo {
>     mut x: i32,
> 
>     // Shadows 'x' and only allows it be be returned
>     prop x: i32 {
>         set => self.x;
>         get {
>             println("Getting x with value \{x}");
>             self.x
>         }
>     };
> }
> 
> 
> mut foo := Foo { x: 42 };
> 
> foo.x = 2;
> ```

> _Note_: While properties may be shadow a field, they must still be uniquely named relative to all other associated items.

## Field property
```
<field-propety> := [ <vis> ] [ 'const' ] [ 'unsafe' ] 'field' 'prop' <ext-name> ':' <type> <prop-body> [ '=' <field-defs> ] [ <field-tag> ]
```

A field property is a special version of a property which is itself also a field.
This is only allowed in non-`extern` [struct types], and must be located immediatally after the fields in the type.
They cannot be defined within an [external impl] or [extension].

To access the underlying field that contains the actual value, the implicit `field` value can be used.

If an accessor is left out, the accessor will map directly to the underlying field.

Similarly to struct fields, each field property may also contain a default value and [field tag]

> _Note_: A field property is syntactic sugar for a field and a property shadowing that field.
>         This also means that the type of the underlying field will be exactly the same as that of the property

> _Example_
> ```
> struct Foo {
>     bar: i32, // regular field
>     field prop baz: i32 {
>         set;
>         get {
>             println("accessing a field property");
>             field
>         }
>     }
> }
> ```

## Static properties [↵](#properties)
```
<static-property> { <attribute> }* [ <vis> ] 'static' 'prop' <ext-name> ':' <type> <prop-body>
```

A static property is a property which operates on [static items] instead of fields.
This means that unlike other properties, these do not have access to `self`.

> _Example_
> ```
> struct Foo {
>     static VALUE: i32 = 0;
> 
>     pub static prop VAL: i32 {
>         get => Self.VALUE;
>     }
> }
> 
> val := Foo.VAL;
> ```


## Trait properties [↵](#properties)
```
<trait-property>               := [ 'const' ] 'prop' <ext-name> ':' <type> <trait-property-accessors>
<trait-property-accessors>     := '{' ? { <trait-propery-accessor> }*, sub-elements need to be unique ? '}'
<trait-property-accessor>      := [ 'const' ] [ 'mut' | 'ref' ] 'get' <trait-property-accessor-body>
                                | [ 'const' ] 'set' <trait-property-accessor-body>
<trait-property-accessor-body> := ';'
                                | <property-accessor-body>
```

A trait property declares an item that is associated with a trait's implementation.
It also declares a set of accessors which any implementation is required to provide.

A trait may provide default implementations for any of the accessors, which will be used when no explicit accessors are implemented.

> _Warning_: While partially implementing the accessors is allowed, this might cause unexpected behavior if when using the property, as the explicit and default implementation might not match.

An implementation is not allowed to add any accessors that are not defined on the trait property, with the exception of any observers.
Observers are solely up to the implementation, meaning the trait cannot require thier existsence.

> _Todo_: Allow for `prop name : ty;`, which will have all accessors

## Constaint [↵](#properties)
```
<constraint-property>           := [ 'const' ] 'prop' <ext-name> ':' <type> <constraint-property-accessors>
<constraint-property-accessors> := '{' ? { <constraint-propery-accessor> }*, sub-elements need to be unique ? '}'
<constraint-property-accessor>  := [ 'const' ] [ 'mut' | 'ref' ] 'get' ';'
                                 | [ 'const' ] 'set' ';'
```

A constraint property is similar to a trait property, except that is does not allow any default accessor implementations.



[constant]:                ./consts.md
[extension]:               ./extensions.md
[external impl]:           ./implementations.md#external-implementations-
[static items]:            ./statics.md
[struct expression]:       ../expressions/constructing-expressions/struct-expressions.md
[field expressions]:       ../expressions/field-access-expressions.md
[method call expressions]: ../expressions/method-expressions.md
[destructor]:              ../type-system/destructors.md
[composite type]:          ../type-system/types/composite-types.md
[struct types]:            ../type-system/types/composite-types/struct-types.md
[field tag]:               ../type-system/types/composite-types/struct-types.md#fields-tags-
[visibility]:              ../visibility.md