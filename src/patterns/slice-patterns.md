# Slice patterns
```
<slice-patter> := '[' <pattern> { ',' <pattern> }* [ ',' ] ']'
```

A slice pattern can match array and slice values that match the defined criteria in the subpatterns.

The form `[..]` is a special form of a slice pattern, which matches a slice of any size.
This pattern is always irrufutable.

A slice pattern is refutable if any of its fields are refutable.
