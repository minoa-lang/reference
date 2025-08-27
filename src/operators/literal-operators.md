# Literal operators

Literal operators are special pseudo-operator that work on literals, and not values.

When calling an operator label on a literal, there may be no space on either side of the `:`.

### Literal operator item [↵](#literal-operators-)
```
<literal-operator-item> := 'literal' 'trait' <name> '(' <type> ')' '->' <type> ';'
```

A literal operator items, as their name implies, declares a new literal operator which can be used to convert a literal to a given value.

These are simpler to declare than an operator set, as they contain fixed functionality and only require the types it operators on to be declared.
It is declared as a special trait using the `literal` weak keyword, which is then proceeded by the name of the trait defining the literal operator.

Between the parentheses, the type of the literal is passed, these are limited to:
- `core:.DecLiteral`
- `core:.DecFloatLiteral`
- `core:.BinLiteral`
- `core:.OctLiteral`
- `core:.HexLiteral`
- `core:.HexFloatLiteral`
- `core:.CharLiteral`
- `core:.StringLiteral`

Corresponding to the type of the available literals.
Finally, the return type of the literal operator is defined.

Using this info, the literal operator will internally create the following trait that is associated with this item:
```
pub trait <name>(<lit-type>) {
    use core:.{Result, CompileError};

    const fn check(lit: <lit-type>) -> Result((), CompileError);
    fn lit_op(lit: <lit-type>) -> <ret-type>;
}
```

Where `<name>`, `<lit-type>`, and `<ret-type>` are replaced by the value they are respective by.

The `check` function is a compile-time function that will run for every use of the literal operator and is required to report any invalid data within the literal.

The `lit_op` function is used to convert the literal type to the return type and is expected to convert the type without any issue.
If this function is run at compile time, it is allowed to panic.

### Builtin literal operators [↵](#literal-operators)

Below is a list of the builtin literal operators:

literal operator | literal kind | resulting type | Info                                   | restrictions
-----------------|--------------|----------------|----------------------------------------|--------------
i8               | Integral     | i8             | 8-bit signed integer literal           | n/a
i16              | Integral     | i16            | 16-bit signed integer literal          | n/a
i32              | Integral     | i32            | 16-bit signed integer literal          | n/a
i64              | Integral     | i64            | 16-bit signed integer literal          | n/a
i128             | Integral     | i128           | 128-bit signed integer literal         | n/a
isize            | Integral     | isize          | machine-sized signed integer literal   | n/a
u8               | Integral     | u8             | 8-bit unsigned integer literal         | n/a
u16              | Integral     | u16            | 16-bit unsigned integer literal        | n/a
u32              | Integral     | u32            | 16-bit unsigned integer literal        | n/a
u64              | Integral     | u64            | 16-bit unsigned integer literal        | n/a
u128             | Integral     | u128           | 128-bit unsigned integer literal       | n/a
usize            | Integral     | usize          | machine-sized unsigned integer literal | n/a
f16              | Float        | f16            | 16-bit floating point literal          | n/a
f32              | Float        | f32            | 32-bit floating point literal          | n/a
f64              | Float        | f64            | 64-bit floating point literal          | n/a
f128             | Float        | f128           | 128-bit floating point literal         | n/a
b                | Character    | u8             | Byte character literal                 | n/a
b                | String       | &[u8]          | Byte string literal                    | n/a
c                | String       | cstr           | C-string literal (null-terminated)     | all characters are required to have a codpoint of <=0x7F
ansi             | String       | str8           | ANSI string literal                    | all characters are required to have a codpoint of <=0x7F
utf7             | String       | str16          | UTF-7 string literal                   | all characters are required to have a codpoint of <=0x7F
utf16            | String       | str16          | UTF-16 string literal                  | n/a
utf32            | String       | str32          | UTF-32 string literal                  | n/a

> _Note_: `Integral` means any of the following: DecLiteral, BinLiteral, OctLiteral or HexLiteral, and
>         `Float` means any of the following: DecFloatLiteral or HexFloatLiteral
