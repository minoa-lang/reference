# When items
```
<when-item>  := 'when' <expr> <item-block> [ 'else' ( <when-item> | <item-block> ) ]
<item-block> := '{' { <item> }* '}'
```

A when item is similar to a [`when` expression](../expressions/when-expressions.md), but can be located at a level where only items are allowed.
Any occurance of `when` in a location expressions are allowed will be a [`when` expression](../expressions/when-expressions.md).

Similarly, it is controlled using a compile time condition, and does not generate a scope, but instead puts its contents into the same scope as the when item.

This can be thought of as containing code marked with the cfg attribute.
