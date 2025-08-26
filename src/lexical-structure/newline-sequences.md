# Newline sequences
```
<new-line> := [ "\r" ] "\n"
```

New line sequences count as whitepace, but are handled differently.
New line have special meaning in the following cases:
- when ending a comment line
- when ending a multi-line string segment

A newline within the file is represented using a newline sequence `\n` (U+000A).
This may also be preceded by a carriage return `\r` (U+000D), any other occurance of a carriage return is ignored by the compiler, but must be retained, meaning that carriage returns will be preserved in any reconstructed file.

Tooling is required to keep the original line ending when not specified otherwise.

When specifified, tooling may:
- convert all line ending to either `\n` or `\r\n` when specified, or
- remove all occurances of `\r` when not part of a line ending when specified
