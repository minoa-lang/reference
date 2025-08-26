```
<item> := <module-item>
        | <use-item>
        | <fn-item>
        | <extern-fn-item>
        | <export-fn-item>
        | <type-alias-item>
        | <struct-item>
        | <union-item>
        | <enum-item>
        | <flag-enum-item>
        | <bitfield-item>
        | <const-item>
        | <static-item>
        | <extern-static-item>
        | <trait-item>
        | <impl-item>
        | <external-block>
        | <extension-item>
        | <constraint-item>
        | <meta-item>
```

An item is a component of a package and are organized within it, inside one of its modules.
Every artifact within the package has a single "outermost" anonymous module; all further items within the package have paths within the package hierarchy.

Items are entirely determined at compile time, generally remain fixed during execution, and may reside in read-only memory.

Some items form an implicit scope for the declarations of sub-items.
In other words, within a function or module, declarations of items can (in many cases) be mixed with the statements, control blocks, and similar components that otherwise compose the item body.
