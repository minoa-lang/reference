# Constructing expressions
```
<constructing-expressions> := <tuple-expr>
                            | <array-expr>
                            | <aggregate-expr>
```

A constructing expression constructs a new instance of a type.
This consists of a group of multiple expressions and can also be used in [in-place operators](../operators/special-operators.md#in-place-operator-).

## Tuple expression [↵](#constructing-expressions)

```
<tuple-expr>     := '(' <tuple-elem> { ',' <tuple-elem> }+ ')'
<tuple-elem>     := [ <tuple-elem-idx> ':' ] <expr>
<tuple-elem-idx> := <int-decimal-literal> | <expr>
```

A tuple expression constructs a tuple value.

The construction exists out of a comma separated list of values that need to be placed within the tuple.
Since 1-ary tuples are not supported, if the expression only contains 1 operand, it will be interpreted as a [parenthesized expression](./paren-expressions.md).
Similarly if the expressions contains 0 operands, a unit type will be created.

The number of operands within the tuple initializer defines the arity of the tuple.
When initializing a tuple, the operand will be evaluated in the order they are written, i.e. left-to-right.
Each operand will be assigned to the field they represent within the expression, i.e. the first operand will be assigned to field '0', and so on.

Tuple values may also be explicitly set using an index for normal tuples, and names for named tuples.
All elements need to be assigned with an index when this method is utilized.

Tuple expressions are value expressions.

##. Array expression [↵](#constructing-expressions)

```
<array-expr>       := <array-list-expr> | <array-count-expr> | <array-list-comprehension>
```

An array expression constructs arrays, and come in 2 forms.

Creating a multi-dimensional array can be done by nesting array expressions within other array expression, i.e. `[[..], [..], [..]]` will result in a 2D array of type `[N][M]T`.

### Array list expression [↵](#array-expression-)
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

### Array count expression [↵](#array-expression-)
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

### Array comprehension [↵](#array-expression-)
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

## Struct expressions [↵](#constructing-expressions)

```
<struct-expr> := <struct-expr-path> '{' [ <struct-expr-member> { ',' <struct-expr-member> }* [ ',' [<struct-complete>] ] ] '}'
<struct-expr-path> := <path> | '.'
<struct-expr-member> := [ <name> ':' ] <expr>
<struct-complete> := '..' [ <expr> ]
```

A struct expression creates a structure, enum, or union value.

### Struct (& union) expression [↵](#struct-expressions-)

A struct expressions with fields enclosed in curly braces allows the specifying of values for each individual field in the structure.

A union is created as a struct expression with only a single field.

#### Functional update syntax [↵](#struct-expressions-)

An struct expression that constructs a value of a struct type can terminate with a `..` followed by an optional expression.

This is used to fill in any fields that were not explicitly specified in the struct expression.

If this expression is given, it must evaluate to a type, which contains fields that match all non-specifier fields in the structure that is being created.
It then moves of copies these missing fields from the value into the structure being created.
As with all struct expressions, all of the fields of the struct must be visble in the current scope, even those not explicitly named.

If no value is provided, all the fields of struct `T` will be extended by their default values declared in `T.default()`, this allows default values to be created at runtime.

Using this expression will also overwrite all default fields.

#### Struct field shorhand [↵](#struct-expressions-)

When initializing an struct value with named fields, it is allowed to write `fieldname` instead of `fieldname: fieldname`.
This allows for a more compact syntax with less duplication.

#### Default fields [↵](#9103-struct-expressions-)

When a struct has default field values, they are not required to assign a value to those fields.
If they are not provided, the default field value will be assigned.

### Tuple struct expression [↵](#9103-struct-expressions-)

An struct expression with fields enclosed in parentheses constucts a tuple struct.
Though listed here as specific experession, this is equivalent to the a call expression to the tuple struct's pseudo-constructor.

####Unit struct [↵](#9103-struct-expressions-)

A unit struct expression creates is either just a path or an implied path.
This refers to the unit struct's implicit constant of it's value.
The unit struct value can also be constructed in 2 ways:
- as a path
- as a fieldless struct expression

## Vector expression
```
<vec-expr> := '[<' <expr> { ',' <expr> }* [ ',' ] '>]'
```

A vector expressions allows the creation of a vector type.

This comes in 2 forms:

### Vector list expression
```
<vec-list-expr> := '[<' <expr> { ',' <expr> }* [ ',' ] '>]'
```

The first form lists out all values in the vector, this is represented by a comma separated list of expressions.
Each expression is evaluated in the order that they are written, i.e. left-to-right.

The number of elements must be less or equal to a supported size by the vector.
This may also be a range expression, in this case, the vector will contain all values within this range.

If less elements are provided than the vector's size, the remaining values will be filled by `T.default()`.

### Vector count expression
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
