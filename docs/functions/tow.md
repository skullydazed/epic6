# tow

## Synopsis

`$tow(<word> <word list>)`

## Technical

Returns the portion of `<word list>` before and including the first complete word literally matching `<word>`. Matching is case-insensitive; neither argument is interpreted as a wildcard pattern. It behaves like finding the word with [`findw`](findw.md), then selecting that many words plus one with [`leftw`](leftw.md).

Parsing and extraction honor [`xdebug dword`](../commands/xdebug.md). When enabled, a double-quoted phrase counts as one word and is dequoted for comparison. When disabled, whitespace separates words and quotation marks are ordinary characters. Quotation marks in the returned slice are retained.

Comparison uses decoded Unicode code points and EPIC's dedicated `en_US.UTF-8` uppercase mapping when available; otherwise non-ASCII code points are unchanged. It is not the server's nickname case mapping, full Unicode case folding, or normalization.

## Practical

Use this function to obtain text through a marker word. [`beforew`](beforew.md) excludes the marker, while [`afterw`](afterw.md) returns text after it. Use [`substr`](substr.md) for substring searching rather than complete-word matching.

The result is a slice of the original argument, not a list rebuilt with single spaces. Internal spacing is retained and trailing separator whitespace is trimmed; extra separator spaces after the marker argument can appear at the beginning. Do not rely on exact surrounding whitespace.

## Returns

The prefix through the first matching word, or empty if the marker is absent, the list is empty, or input is missing. A match at the first list word returns that word. The function does not modify variables. There are no options or server requirements.

## Examples

| Expression | Result |
| --- | --- |
| `$tow(foobar one two foobar my shoe)` | `one two foobar` |
| `$tow(booya one two foobar my shoe)` | Empty string |
| `$tow(WORD a WoRd list)` | `a WoRd` |
| `$tow(word word later)` | `word` |

With `xdebug dword` enabled, `$tow("two words" one "two words" three)` returns `one "two words"`.

## History

First appeared in “plus 2” (post-ircII, pre-EPIC).

## Source

Handler: [`source/functions.c`](../../source/functions.c:3285). Word search: [`function_findw`](../../source/functions.c:5492). Extraction and parsing: [`include/words.h`](../../include/words.h:99), [`source/words.c`](../../source/words.c). Case mapping: [`source/recode.c`](../../source/recode.c:1173).
