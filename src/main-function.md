# Main function

The main function, unlike langauge builtins, is a special function that the user can themselves define.
It must appear in the root module of a binary, i.e. in `main.`.

The main function takes no arguments, and must return a type implementing the `Termination` trait.
This is meant to allow the main function to return any type that implements the trait.

Some examples of possible main functions are
```
fn main() {}
```
```
fn main() -> ! {

}
```
```
fn main() -> impl Termination {

}
```

> _Todo_: Make sure examples are valid, i.e. fix implementation

The main function may be an import from another library or a module in the current library by aliasing a function meeting the requirements as `main`.

```
mod foo {
    fn bar() {
    }
}

use :.foo.bar as main;

```
