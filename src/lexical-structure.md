# Lexical structure

This section contains information about the lexical structure of a code file.
This consists of a list of tokens with their associated _trivia_.

A token is a primitive in the gramar of a language.
Tokens are produced from the incoming source file.
In addition to elements in this section, literals are also interpreted as tokens, but infomation about them is located within their own [section].

_Trivia_, in this case any whitespace, newlines, or comments, are generally not interpreted as token, but as metadata that is associated with a given token.

In general, each token will only have the trivia preceeding it associated with it, but there are a number of exceptions:
- Any top-level documentation located at the beginning that the file will not be associated with a token, but instead with the file
- Any top-level documentation defined inside an item will be associated with the preceeding token
- Any suffix description comment will be associated with the preceeding token
- Any comment after the last token will be associated with the file

> _Note_: The langauge is designed in such a way, that each line can independently be parsed into a set of tokens without needing to have any context from the surrounding lines




[section]: ./literals.md