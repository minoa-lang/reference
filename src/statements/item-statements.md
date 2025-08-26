# Item statements
```
<item-stmt> := <item>
```

An item statement is a items which can be defined within a block.
Declaring them at a location other than inside a module, and limits their definition to the block they belong to.
As such, they cannot be referenced outside of the specific scope they are declared in.

They may access items declared outside of the item, but may not access any locally declared values from the scope they are located in.
They can also implicitly capture generics from an outer scope, unless they are shadowed by the generic with the same name by the item.
