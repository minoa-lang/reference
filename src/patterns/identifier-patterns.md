# Identifier patterns
```
<iden-pattern> := [ 'ref' ] [ 'mut' ] <name> [ '@' <pattern-no-top-alt> ]
```

Identifiers patterns bind the value they match to to a variable with the same name, and will be located in the [value namespace] the binding is located in.
This name must be unique within the pattern.

This binding (the newly created value) is allowed to shadow any variable within the current namespace.
It's scope is dependent on that of the pattern it is located in (such as a `let` bindings or `match` arm)

By default, identifiers patterns bind a variable to a copy or move from the matched value, depending on how whether they implement `Copy`.

Each identifier pattern may have an associated pattern, which specifies a subpattern to which the identifier must adhere to to result in a match.

> _Note_: An alternative pattern is not allowed to be directly used, since `a @ 2 | 3` will be interpreted as `(a @ 2) | 3` instead of `a @ (2 | 3)`

> _Example_
> ```
> match a {
>     // bind the value of `a` to `val`, if it matches the sub-pattern
>     val @ 1..=5 => println("value in range [1, 5]: \{val}"),
>     other       => println("Other value: \{other}"),
> }
> ```

Some additional modifiers may be added to the pattern, modifying how the value is bound and how the resulting variable:
- `mut`: makes the resulting variable mutable
- `ref`: takes a reference to the bound value instead of copying/moving the value of it.
         The `ref` keyword must be used instead of a `&`, as the latter is used to destructure a reference.

  > _Example_: `ref` vs [reference pattern]
  > ```
  > val := 10;
  > a: ?&i32 = &val;
  > 
  > // Takes a reference to the reference to the value in `a`
  > match a {
  >     .None => (),
  >     .Some(ref val) => (),
  > }
  >
  > // removes the reference from `a`, and copies the inner value
  > match a {
  >     .None => (),
  >     .Some(&val) => (),
  > }
  > ```

- `ref mut`: takes a mutable reference to the bound value, allowing mutation of the value within the scrutinee

> _Example_
> ```
> // moves the value out of `a`
> match a {
>     .None => (),
>     .Some(val) => (),
> }
> 
> // moves the value out of `a`, but allows mutation of the resulting `val` variable
> match a {
>     .None => (),
>     .Some(val) => (),
> }
> 
> // takes a reference to the inner field bound to `val` inside of `a`
> match a {
>     .None => (),
>     .Some(ref val) => (),
> }
> 
> 
> // takes a mutagble reference to the inner field bound to `val` inside of `a`, allowing mutation of it
> match a {
>     .None => (),
>     .Some(ref mut val) => (),
> }
> ```

When an identifier pattern may also be interpreted as a [path pattern]

Whenever a binding is defined as either `ref` or `mut ref`, it may **not** shadow any constant values within the scope.

By default, an identifier pattern is irrufutable, unless the subpattern provided is itself refutable.

## Binding modes [↵](#identifier-patterns)

To help with ergonomics, identifier patterns can operate in different binding modes, depending on the surrounding context.
The binding mode is dependent on the value being matched, the patter, and the explicitly defined binding mode.

> _Note_: While the binding mode is error checked for any pattern, it will only have behavioral changes whenever an identifier patterns is used

Determining which binding mode can be done using the method defined [below].

To avoid the default binding mode being something other than `move`, the value being matched against can either be dereferenced before matching, or have a [reference pattern] as an outer pattern.

_Example_
```
val := 10;
a: &i32 = &val;

// derefencing `a` will result in `val` having its value moved into it
match *a {
    val => (),
}

// the surrounding reference pattern makes the inner `val` result in having its value moved into it
match a {
    &val => (),
}
```

The binding mode may also be defined explicitly by utilizing either `mut`, `ref` or `ref mut`, these will result in the following binding modes:
- `mut`: move or copy
- `ref`: immutable reference
- `ref mut`: mutable reference

However, these explicit binding modes are only allowed whenever the current default binding mode is `move`, in any other cases, it will result in an error

> _Example_: Explicit binding mode with a default binding mode of 'by reference'
> ```
> let [mut a] = &[()];     // error: cannot have an explicit mutable 'move' within a default 'by reference' binding mode context
> let [ref a] = &[()];     // error: cannot have an explicit 'by reference' mode within a default 'by reference' binding mode context
> let [ref mut a] = &[()]; // error: cannot have an explicit 'by mutable reference' mode within a default 'by reference' binding mode context
> ```
> This can be gotten around by explicitly placing the pattern in a reference pattern
> ```
> let &[mut a] = &[()];
> ```

Similarly, a [reference pattern] may only appear when the default binding mode is 'move'

> _Example_
> ```
> let [&x] = &[&()]; // error: cannot have an explicit reference pattern within a default `by reference` binding mode
> ```

Both 'move' and either 'by reference' bindings may be mixed within a single pattern.
This will result in a partial move, where the 'move' field cannot be accessed, nor the value containing them as a whole.

> _Example_
> ```
> // `name` is bound by 'move', and `age` is bound by 'by reference'
> let Person{ name, ref age } = person;
> 
> // error: `person` has been partially moved out of
> // _ = &person;
> 
> // error: `Person.name` has been moved out of `person`
> // _ = &person.name;
> 
> // allowed, as it only has a reference taken, but cannot be moved out of, as the reference still exists
> _ = &person.age;
> ```

### Determining the default binding mode [↵](#identifier-patterns)

To determine the default binding mode for each pattern, the following process is used.

For determining these, the following patterns are defined as being non-reference patterns:
- bindings, i.e. identifier patterns
- [wildcard pattern]
- [`const` pattern] of a reference type
- [reference pattern]

The default binding mode start off as 'move`.
Then starting from the outer most pattern each patterns is set to the current binding mode, before any inner binding mode is determined.

For each subpattern, the following is decided:
- if a referenced value is matched to an outer non-reference, for every reference in the value:
  1. update the default binding mode
    - if the value is a mutable reference, and the default mode is 'move', update the default mode to 'by mutable reference'
    - if the value is a shared reference, and the default mode is 'move', update the default mode to 'by reference'
    - otherwise the default mode will remain unchanged
  2. dereference the value, if the dereferenced value is still a reference value, repeat this process with the dereferenced value
- otherwise, the default mode will be set to 'move'




[below]:             #determining-the-default-binding-mode-
[path pattern]:      ./path-patterns.md
[reference pattern]: ./reference-patterns.md
[wildcard pattern]:  ./wildcard-patterns.md
[`const` pattern]:   ../patterns.md#constant-patterns-
[value namespace]:   ../namespaces-scopes.md#namespaces