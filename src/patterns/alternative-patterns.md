# Alternative patterns
```
<alt-pattern> := <pattern-no-top-alt> { | <pattern-no-top-alt> }*
```

An alternative pattern is a collection of patterns, where only one needs to match.
When this pattern is used, identifier patterns are only allowed if all subpatterns contain the identifier, and if all of them have the same type.

## Rules alternative patterns [↵](#alternative-patterns)

To allow for an alternative pattern `p | q`, `p` and `q` need to follow a set of rules

1. When assuming that type convertions are not allowed, and they are unified in an exact manner, the pattern is ill-formed when:
    - the type inferred for `p` does not unify with the type inferred for `q`
    - the same set of bindings are not introduced in `p` or `q`
    - the type of any 2 bindings with the same names in both `p` and `q` do not unify witheachother in respect of them having:
        - different binding modes
        - different types
2. When checking an expression matching `match e_s { a_1 => e_1, ..., a_n => e_n }`, for each arm `a_i` which contains a pattern in the form `p | q`,
   is considered ill-formed when, at any depth `d` where the pattern of `e_s` at depth `d`, the type of the expression does not unify to `p_i | q_i`.
3. When it comes to deciding the exhaustiveness of a pattern, it can be transformed to a top level-only alternative pattern.
   This can be done for any pattern contain a sub-pattern, e.g. the pattern `a(p | q, ..rest)` can be transformed to `a(p, ..rest) | a(q, ..rest)`.
