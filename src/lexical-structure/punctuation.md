
# Punctuation
```
<punct> := ? any unicode point within , except for <reserved-punct> ?
<reserved-punct> := <delimiters> | "'" | '"'
```

Punctuation are sequences of character that cannot be interpreted as any other token.
There is no fixed set of allowed punctuation.

There is also a set of characters that are not allowed in punctuation, as they each have their own special meaning.
These are `'` and `"`.

> _Todo_: Limit possible punctuation symbol to fix set of common, but distinguishable symbols
