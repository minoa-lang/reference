# Source code representation

This section contains info about the source code representation in the file, and by extension about the data on disk.

## Input format [↵](#source-code-representation)

Each source input is interpreted as a sequence of Unicode codepoints encoded within the utf-8 format.
If an input does not contain a valid utf-8 sequence, this will result in an error.

Source input is generally represented as a sequence of bytes in a file, but can also come from other sources, some examples of which are:
- an interactive programming enviornment (a so-called "Read-Evaluate-Print-Loop" or REPL),
- a database
- a memory buffer of an IDE
- command-line arguments
- etc.

Minoa source files use the extension `.mn`

> _Implementation note_: Implementations may support additional encoding schemes, such as UTF-16, UTF-32, or non-unicode character mappings.

## Byte order marker [↵](#source-code-representation)
```
<byte-order-marker> := "\xEF\xBB\xBF"
```

The file may begin using a byte order marker, this marker is kept track of, but is generally ignored by the compiler.
The utf-8 byte order marker does not encode the order, as utf-8 work in single byte units and can therefore not be in a different marker.
It is mainly there to indicate the that content of this file encodes a utf-8 sequence, preventing it to be interpreted as another text encoding.

If the file would be reconstructed from its lexical representation, the file will be rebuilt to include the byte order marker if it was present before.

The utf-8 byte order marker is the following: `EF BB BF`.

Any other byte order marker is invalid and will produce an error, as the text file would represent another text encoding.
The disallowed byte order markers are the following:

Encoding    | Representation
------------|----------------
utf-16 (be) | FE FF
utf-16 (le) | FF FE
utf-32 (be) | 00 00 FE FF
utf-32 (le) | FF FE 00 00
utf-7       | 2B 2F 76
utf-1       | F7 64 4C
utf-ebcdic  | DD 73 66 73
scsu        | 0E FE FF
bocu-1      | FB EE 28
gb18030     | 84 31 95 33

If any of the above markers is found, a error will be generated and notify which invalid bytecode marker is used.

> _Implementation note_: If the implementation support additional encoding schemes, it must support the respective byte order markers and **not** generate an error.

## Shebang [↵](#source-code-representation)
```
<shebang> := '#!' ? any valid character ? <newline>
```

A file may contain a shebang in the first line in a file, but will be ignored (and preserved) by the compiler.

## Normalization [↵](#source-code-representation)

Source files are normalized using the Normalization Form C (NFC) as defined in [Unicode Standard Annex #15].
It is generally expected that the source code is stored within a normalized form.
As this cannot be guaranteed, the compiler will automatically try to convert unnormalized text into normalized text.
In case a non-normalized character sequence is detected, a warning will be emitted.

If the input is guaranteed to be normalized, this can be turned off.

An exception to normalization is [string literals], in which no normalization happens.

> _Note_: Normalization Form D is more uniform, in that characters are always maximally decomposed into combining characters; in NFD, characters may or may not be decomposed depending on whether a composed form is available. NFD may be more suitable for certain uses such as type correction, homoglyps detection, or code completion. But NFC is also more common and recomended in locations, it's also more commonly used for languages that support unicode.

> _Implementation note_: Implementations may support NFD, but this must be done using an explicit compiler flag, and must in addition also be able to reconstruct the file in NFC mode.
>                        Any symbol output by the compiler, must adhere to NFC.

> _Implementation note_: It is recommended, but not required, for the compiler to detect homoglyphs and confusables, as defined in the ['Confusable Detection' section of Unicode Annex #39].
>                        The compiler is allowed to convert these to a single character, but this must only be enabled when an explicit flag is provided.

> _Implementation note_: A quick detection optimization is defined within the ['Quick Check for NFC' section in Unicode Annex #15].

# Disallowed characters [↵](#source-code-representation)

Minoe forbids a certain set of characters from being used within any input.
These are the following (ranges are inclusive):
- U+0000 to U+0008
- U+000E to U+001F
- U+007F

Any occurance of these characters will result in an error.

## Reconstruction [↵](#source-code-representation)

Since the compiler keeps all elements, with the exception of normalization, it can output the identical file that was passed to it from only the tokens and their respective _trivia_.

> _Implementation note_: This is an optional feature and is not required for a conforming compiler implementation


[Unicode Standard Annex #15]:                          https://www.unicode.org/reports/tr15/tr15-56.html
[string literals]:                                     #64-string-literals-
['Confusable Detection' section of Unicode Annex #39]: https://www.unicode.org/reports/tr39/#Confusable_Detection
['Quick Check for NFC' section in Unicode Annex #15]:  https://unicode.org/reports/tr15/#NFC_QC_Optimization