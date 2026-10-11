# beforew function

## Synopsis

```text
$beforew(word word-list)
```

## Technical

`beforew()` returns the portion of `word-list` before the first complete word matching `word`, excluding the matching word. Comparisons are case-insensitive and literal: the list is not interpreted as wildcard patterns, and there is no “best pattern” selection or count argument.

If the marker or list is missing, the marker is absent, or the first list word matches, the result is the empty string. Matching uses [`findw()`](findw.md), which searches from the beginning and stops at the first match.

Parsing and extraction honor [`xdebug dword`](../commands/xdebug.md). With that flag enabled, a double-quoted phrase counts as one word and its surrounding quotes are removed for comparison. With it disabled, whitespace separates words and quotes are ordinary characters. Quotes in the returned portion are retained.

Case comparison decodes UTF-8 and uses EPIC's `en_US.UTF-8` uppercase mappings when that locale is available. Otherwise non-ASCII code points are compared unchanged. The comparison does not use the server's nickname case mapping.

## Practical

Use this function to obtain everything before a marker word. The result is a slice of the original text, so spacing between returned words is retained rather than rebuilt with single spaces. Trailing separator whitespace is trimmed; extra spaces between the marker argument and the first list word can remain at the beginning. Do not rely on exact surrounding whitespace.

See [`tow()`](tow.md) to include the matching word, [`afterw()`](afterw.md) for text after it, and [`before()`](before.md) for character-based matching. [`leftw()`](leftw.md) extracts by word count instead of finding a marker.

## Returns and examples

| Expression | Return value |
| --- | --- |
| `$beforew(foobar one two foobar my shoe)` | `one two` |
| `$beforew(booya one two foobar my shoe)` | Empty string |
| `$beforew(foobar o* t* f* m* s*)` | Empty string: no literal `foobar` |
| `$beforew(booya o* t* f* m* s*)` | Empty string |
| `$beforew(foobar f* fo* foobar foo* foob*)` | `f* fo*` |
| `$beforew(WORD a WoRd list)` | `a` |
| `$beforew(word word later)` | Empty string |
| `$beforew()` | Empty string |

With `xdebug dword` enabled, `$beforew("two words" one "two words" three)` returns `one`.

## History

This function first appeared in “plus 2” (post-ircII, pre-EPIC). The current implementation uses literal word comparison rather than the older wildcard-pattern behavior.

## Source

[source/functions.c](../../source/functions.c) registers `beforew` and implements `function_beforew()` through `function_findw()`. [include/words.h](../../include/words.h), [source/words.c](../../source/words.c), and [source/ircaux.c](../../source/ircaux.c) implement argument parsing, extraction, and comparison. [source/recode.c](../../source/recode.c) supplies uppercase mappings.
