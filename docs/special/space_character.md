# Space characters

In EPIC's ordinary word and argument parsing, a *space* is a byte classified as whitespace by the system's `isspace(3)` function. This includes the ordinary space (codepoint 32) and tab (codepoint 9), as well as line feed (10), vertical tab (11), form feed (12), and carriage return (13). Additional byte values can be classified as whitespace by the active locale.

EPIC initializes its locale from the process environment, then fixes numeric formatting to the `C` locale. Do not assume that every machine classifies additional bytes identically.

## Byte classification and Unicode

`isspace` is byte-oriented. It does not decode a UTF-8 character and then classify its Unicode codepoint. In particular, a Unicode character that visually resembles a space is not automatically an EPIC word separator. The limitation is broader than codepoints above 255: even a codepoint below 256 can require multiple UTF-8 bytes, such as a nonbreaking space.

For portable command and script separators, use the ordinary ASCII space. To construct one explicitly in an expansion, use `$chr(32)`; `$chr(9)` produces a tab. See [chr](../functions/chr.md).

## Context matters

This definition describes shared word-parsing helpers, not a universal rule for all syntax. Ordinary argument extraction skips leading whitespace and finds word boundaries using these byte tests. Whether double quotes group a phrase into one argument depends on the particular command or function's argument parser; whitespace classification alone does not provide quoting.

The [expression](expressions.md) lexer explicitly skips ASCII whitespace bytes 9 through 13 and 32 between tokens. Other operations can use their own delimiters or match literal characters. A space inside a string can be data rather than a separator. Consult the relevant command or function documentation before substituting tabs or other whitespace for an ordinary space.

## Source references

- `include/ircaux.h`: `my_isspace` maps to the system `isspace` function.
- `source/ircaux.c`: shared argument extraction and whitespace-only string checks.
- `source/words.c`: word-boundary movement and extraction.
- `source/expr2.c`: explicit ASCII whitespace cases in the expression lexer.
- `source/irc.c`: startup locale initialization.
- `source/functions.c`: `chr` constructs UTF-8 characters from codepoints.
