# Extensions
```
<extension-item> := { <attribute>* } [ <vis> ] 'extension' [ <generic-params> ] <type> 'as' <name> [ <where-clause> ] '{' { <assoc-item> }* '}'
```

Extensions allow functionality to be added to am external type, including allow traits to be implemented that when the implementation would not be coherent.
They must be implemented on an external type, adding an extension to a type declared within the current library will result in a compiler error.

Extensions are named and need to be imported explicitly to be able to use any functionality declared in them.

When importing conflicting extensions, meaning that any item within the extension conflicts will also result in a compiler error.
Item are conflicting when:
- the item was implemented in the library the type was defined
- a trait implementation was implemented in the library defining the trait
- the item conflict with an item implemented in another currently imported extension

## Trait extentions [↵](#extensions)

An extension is a trait extension when the type refer to a given trait.
Doing this allows additional functionality to be added to any type that implements the trait.

This comes a the caveat that all extensions need to have an implementation withing the extension.
This is because when a trait is used as a [trait object type](../type-system/types/trait-object-types.md), the extension is not stored within the object's vtable, but is instead placed in a function utilizing the interface that is provided by the trait object itself.
