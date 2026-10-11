# fromw function

## Synopsis

`$fromw(<word> <word list>)`

## Technical

Returns the portion of the word list starting at its first complete word matching `word`, including the matching word. Matching is case-insensitive and literal: wildcard patterns and substrings have no special meaning. There is no count argument.

The function behaves like locating the marker with [$findw()](findw.md) and extracting the list from that position with [$restw()](restw.md). An absent marker, missing input, or an empty list returns the empty string. Repeated markers select the first occurrence.

Parsing and extraction honor [/xdebug dword](../commands/xdebug.md). With that flag enabled, a double-quoted phrase counts as one word, and surrounding quotes are removed for comparison. With it disabled, whitespace separates words and quotes are ordinary characters. Quotes in the extracted portion are retained.

Case comparison decodes UTF-8 and uses EPIC's `en_US.UTF-8` uppercase mappings when that locale is available. Otherwise non-ASCII code points are compared unchanged. This does not use the server's nickname case mapping.

## Practical

Use this function to retain a marker and everything following it. The result is a slice of the original argument text, rather than a list rebuilt with single spaces. Internal spacing is retained and trailing whitespace is trimmed. Extra separator spaces before the selected word can remain at the start; do not rely on exact surrounding whitespace. The original list is not modified.

See [$afterw()](afterw.md) to exclude the marker, [$beforew()](beforew.md) for the preceding portion, and [$tow()](tow.md) for the preceding portion including the marker.

## Returns and examples

| Expression | Result |
| --- | --- |
| `$fromw(foobar one two foobar my shoe)` | `foobar my shoe` |
| `$fromw(booya one two foobar my shoe)` | Empty string |
| `$fromw(foobar f* fo* foobar foo* foob*)` | `foobar foo* foob*` |
| `$fromw(WORD a WoRd list)` | `WoRd list` |
| `$fromw(word word one word two)` | `word one word two` |
| `$fromw(word a word)` | `word` |
| `$fromw()` | Empty string |

With `xdebug dword` enabled, `$fromw("two words" one "two words" three)` returns `"two words" three`.

## History

First appeared in plus-2, after ircII and before EPIC. Before EPIC4-1.1.10, the list was interpreted as wildcard patterns; the current implementation compares literal words.

## Source

Registration and `function_fromw()` using `function_findw()`: [source/functions.c](../../source/functions.c). Word-mode selection and extraction: [include/words.h](../../include/words.h) and [source/words.c](../../source/words.c). Case comparison: [source/ircaux.c](../../source/ircaux.c). Uppercase mappings: [source/recode.c](../../source/recode.c).
