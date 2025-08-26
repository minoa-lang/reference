# Properties
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

## Getters [↵](#properties)

Getters are used to retrieve a value from the property.

They come in 3 flavors, each having access to the type containing in a different way, as described below:

getter    | kind      | `self` access | return   | remarks
----------|-----------|---------------|----------|----------------------------------
`get`     | value     | `self`        | `T`      |requires the value to be `Clone`.
`ref get` | reference | `&self`       | `&T`     |
`mut get` | mutable   | `&mut self`   | `&mut T` |

## Setters [↵](#properties)

A setter is used to modify the value of a property, without needing a mutable reference to the value

A setter is provided with a name surrounded by `()` as an argument, and has access to self as `&mut self`

#### Setter shorthand [↵](#7112-setters-)

A setter does not require an explicit name to be provided for the value that it is being set to.
In this case, the implicit `$0` can be used.

## Observers [↵](#properties)

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

## Direct-bind properties [↵](#properties)
```
<property-direct-bind>         := { <attribute> }* [ <vis> ] [ 'unsafe' ] 'property' <name> '{' { <property-direct-bound-get-det> }[1,4] '}' ':=' <name> ';'
<property-direct-bind-get-set> := [ <vis> ] [ [ 'mut' ] 'ref' ] 'get'
                                | [ <vis> ] 'set'
```

A direct-bind property is a special version of a property that is directly bound to a value inside of the type it's implemented on.

This allows for access to a member of the type, but also allows access to be restricted based on the property definition.

> _Todo_: allow direct binds to be used in a struct expression, instead of the bound variable (both work, but only one of them)

## Get-only properties [↵](#properties)

Get-only properties is some convenience syntactic sugar when a property is only expected be accessed via a `get`.
In this case, the property is directly followed by a block expressions that generates the return value.

## Trait properties [↵](#properties)

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
