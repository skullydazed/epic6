# findws function

## Synopsis

`$findws(<word> <word list>)`

## Technical

Searches the word list for every occurrence of `word` and returns all matching positions, counting from zero. Use this instead of [$findw()](findw.md) when the list may contain duplicates.

Matching compares complete words case-insensitively. Wildcard characters are literal, and substrings do not count: `hmm` does not match `hmmm`. The returned positions follow the list's original order. The function does not modify the input list.

The search word and list use normal function word parsing. When [/xdebug dword](../commands/xdebug.md) is enabled, a double-quoted phrase counts as one word and surrounding quotes are removed for comparison. With that setting disabled, whitespace separates words and quotes are ordinary characters.

Case comparison decodes UTF-8 and applies uppercase mappings using EPIC's `en_US.UTF-8` locale when available. If that locale cannot be created, non-ASCII code points are compared unchanged. This uses neither server nickname case mapping nor locale collation order.

## Returns

A space-separated list of all matching zero-based positions. Returns the empty string when no word matches, the list is empty, or function input is missing. Unlike `$findw()`, a failed search does not return `-1`.

## Examples

| Expression | Result |
| --- | --- |
| `$findws(very epic is a very very scriptable client)` | `3 4` |
| `$findws(foo FOO bar foo)` | `0 2` |
| `$findws(hmm blah hmm foo bar hmmm)` | `1` |
| `$findws(foo foobar bar)` | Empty string |
| `$findws(word)` | Empty string |
| `$findws()` | Empty string |

```irc
assign test blah hmm foo bar hmm
fe ($findws(hmm $test)) x {
    echo $word($x $test)
}
```

This prints `hmm` twice, at positions `1` and `4`. With `xdebug dword` enabled, `$findws("two words" one "two words" "two words")` returns `1 2`.

## Source

Registration and `function_findws()`: [source/functions.c](../../source/functions.c). Function word parsing: [include/words.h](../../include/words.h). UTF-8 comparison: [source/ircaux.c](../../source/ircaux.c). Locale-based uppercase mapping: [source/recode.c](../../source/recode.c).
