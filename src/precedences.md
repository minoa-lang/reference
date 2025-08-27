# Precedences

Precedence defines the order in which expressions are evaluated, and is used to decide the order when multiple.
It is used to define which expressions have a higher priority than others, and those expression will be applied first.
Parentheses can be used to explictl change the order, as they have the highest precedence.

Another feature of precendence is the associativity.
When multiple expressions are chained, associativity defines which side has the higher 'precedence', i.e. how expressions are grouped together.

For example, the expression `a + b + c` can be written as either `(a + b) + c` or `a + (b + c)`.
While this doesn't always have an impact on the result generated, it should be assumed that the order can have an impact.
Each order could not only result in an actual difference in value, but even in type the expression will result, or in worse cases, fail to compile the underlying code.

For limitation on the naming, check the [precedence scoping and use](#153-precedence-scoping-and-use) section.

## Built-in precedences [↵](#precedences)
                       
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

## User-defined precedence [↵](#precedences)

```
<precedence-item> := 'precedence' <name> '{' { <precedence-member> }* '}'
<precedence-member> := 'higher_than' ':' <name>
                     | 'lower_than' ':' <name>
                     | 'associativity' ':' ( 'left' | 'right` | 'none' )
```

A precedence item can be used to define a custom precedence of user-defined operators.

### Precendence order [↵](#user-defined-precedence-)

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

### Associativity [↵](#user-defined-precedence-)

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

## Precedence scoping and use [↵](#precedences)

```
<precedence-use> := 'precedence' 'use' <use-root> [ '.' '{' <name> { ',' <name> }* [ <name> ] '}' ] ';'
```

Precedences have some special scoping rules, as they are not scoped relative to the module that contains them, but they are exclusivly at the top level of a library.
This means that a library may not contain 2 precedences with the same name, no matter if they are in a nested module or not.

Precedences also are not imported from other files using a standard use declaration, but are instead imported by a special 'precedence use'.
Precedence uses declare a use root defining where the precedences are located, followed by an optional list of specific precedences to include.
Unlike precedence items which can be defined within a nested module, precedence uses are required to be within the main file of the library, i.e. in either the `main.mn` or `lib.mn` root, and must not be nested within a module in tht file.

When a precedence is imported, its name may not conflict with those of any other precedence declared within the library or imported from an external library.
