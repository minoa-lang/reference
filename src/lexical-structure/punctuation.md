
# Punctuation
```
<punct>          := <punct-head> { <punct-continue> }*
<punct-head>     := '!'
                  | '%'
                  | '&'
                  | '*'
                  | '+'
                  | '-'
                  | '.'
                  | '/'
                  | '<'
                  | '='
                  | '>'
                  | '?'
                  | '|'
                  | '~'
                  | ? U+00A1 - U+00A7 ?
                  | ? U+00A9 ?
                  | ? U+00AB ?
                  | ? U+00AC ?
                  | ? U+00AE ?
                  | ? U+00B0 ?
                  | ? U+00B1 ?
                  | ? U+00B6 ?
                  | ? U+00BB ?
                  | ? U+00BF ?
                  | ? U+00D7 ?
                  | ? U+00F7 ?
                  | ? U+2016 ?
                  | ? U+2017 ?
                  | ? U+2020 - U+2027 ?
                  | ? U+2030 - U+203E ?
                  | ? U+2041 - U+2053 ?
                  | ? U+2055 - U+205E ?
                  | ? U+2190 - U+23FF ?
                  | ? U+2500 - U+2775 ?
                  | ? U+2794 - U+2Bff ?
                  | ? U+2E00 - U+2E7F ?
                  | ? U+3001 - U+3003 ?
                  | ? U+3008 - U+3020 ?
                  | ? U+3030 ?
<punct-continue> := <operator-head>
                  | ? U+0300 - U+036F ?
                  | ? U+1DC0 - U+1DDF ?
                  | ? U+20D0 - U+20FF ?
                  | ? U+FE00 - U+FE0F ?
                  | ? U+FE20 - U+FE2F ?
                  | ? U+E0100 - U+E01EF ?

```


Punctuation is any sequence of characters that adheres to the allowed set of characters.

This set constist out of most, but not all, ASCII punctuation, and unicode characters within the given ranges.
These unicode ranges consists out of:
- Mathematical operators
- Miscellaneous symbols
- Dingbats
- etc

> _Note_: For more info about which punctuation is not allowed as an operator, can be found [here](../operators.md#disallowed-operators-).
