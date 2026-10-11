# afterw function

## Synopsis

```text
$afterw(word word-list)
```

## Defined behaviors

`afterw()` returns the portion of a word list after the first word matching `word`. The matching word is excluded. Matching compares complete words case-insensitively, without wildcard interpretation. There is no count argument.

| Expression | Return value |
| --- | --- |
| `$afterw()` | Empty string |
| `$afterw(word)` | Empty string |
| `$afterw(word some list)` | Empty string |
| `$afterw(word a word list)` | `list` |
| `$afterw(WORD a WoRd list)` | `list` |
| `$afterw(word word one word two)` | `one word two` |

Like [`findw()`](findw.md), this function honors [`xdebug dword`](../commands/xdebug.md). When that flag is enabled, a double-quoted phrase counts as one word, and its surrounding quotes are removed for comparison. When disabled, ordinary whitespace separates words and quotes are ordinary characters. Extraction uses the same word mode. Quotes in the returned portion are retained.

Case comparison decodes UTF-8 and applies uppercase mappings using EPIC's `en_US.UTF-8` locale when available. If that locale cannot be created, non-ASCII code points are compared unchanged. This does not use the server's nickname case mapping or the user's locale collation rules.

## Practical

Use this function to extract the remainder of a list after a marker word. If the marker is absent, required parameters are missing, or the marker is the last word, it returns the empty string.

The returned portion is extracted from the original argument text, rather than rebuilt with single spaces. Internal spacing is retained; trailing whitespace is trimmed. Extra separator spaces immediately after the marker can remain at the start of the result, so do not rely on exact surrounding whitespace.

See [`restw()`](restw.md) for extraction by word index, [`fromw()`](fromw.md) to include the matching word, and [`after()`](after.md) for character-based delimiters.

## Examples

```text
$afterw(foobar one two foobar my shoe)    returns "my shoe"
$afterw(booya one two foobar my shoe)     returns an empty string
```

With `xdebug dword` enabled:

```text
$afterw("two words" one "two words" three)    returns "three"
```

## History

This function first appeared in “plus 2” (post-ircII, pre-EPIC). It formerly used wildcard matching; the current implementation compares literal words.

## Source

[source/functions.c](../../source/functions.c) registers `afterw` and implements `function_afterw()` using `function_findw()`. [include/words.h](../../include/words.h), [source/words.c](../../source/words.c), and [source/ircaux.c](../../source/ircaux.c) implement argument parsing, extraction, and case-insensitive comparison. [source/recode.c](../../source/recode.c) supplies the locale-based uppercase mapping.
