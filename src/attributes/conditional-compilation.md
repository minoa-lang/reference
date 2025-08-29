# Conditional compilation attributes

## `cfg` [↵](#conditional-compilation-attributes)

The `cfg` attribute can be used to change the what code is compiled when certain configuration condtions are matched.
The `cfg` attribute is similar to the [`when` expression], but is only allowed to access configuration values, these can be combined with lazy boolean operators and the not operator to define the condition for when the code should be compiled in.

## `cfg_attr` [↵](#conditional-compilation-attributes)

The `cfg_attr` attribute can be used to change whether an attribute is applied when certain configurations are matched.
The `cfg_attr` is similar to the `cfg` attribute, but instead of being applied to the element below it, it has a second paramter containing the actual attribute that it represents



[`when` expression]:                       ./expressions/when-expressions.md