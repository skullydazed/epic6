# findw function

## Synopsis

`$findw(<word> <word list>)`

## Technical

Searches the word list for the first occurrence of `word` and returns its position, counting from zero. It compares complete words case-insensitively; wildcard characters are literal and substring matches do not count. Repeated matches return the first position only; see [$findws()](findws.md) for multiple positions.

The search word and list use normal function word parsing. When [/xdebug dword](../commands/xdebug.md) is enabled, a double-quoted phrase counts as one word and surrounding quotes are removed before comparison. When disabled, whitespace separates words and quotes are ordinary characters.

Case comparison decodes UTF-8 and applies uppercase mappings using EPIC's `en_US.UTF-8` locale when available. If that locale cannot be created, non-ASCII code points are compared unchanged. This uses neither the server's nickname case mapping nor locale collation order.

## Practical

Use this function to locate a marker or retrieve a word by its position. [$beforew()](beforew.md) and [$afterw()](afterw.md) extract the portions before or after the first matching word. The input list is not modified.

## Returns

A nonnegative, zero-based word position on a match, or `-1` when the supplied word is not in the list, including an empty list. Missing function input returns the empty string. Check for a nonnegative result: `0` is a valid match and `-1` is not success.

## Examples

| Expression | Result |
| --- | --- |
| `$findw(very epic is a very scriptable client)` | `3` |
| `$findw(VERY epic is a very scriptable client)` | `3` |
| `$findw(foo foo bar foo)` | `0` |
| `$findw(foo foobar bar)` | `-1` |
| `$findw(word)` | `-1` |
| `$findw()` | Empty string |

With `xdebug dword` enabled, `$findw("two words" one "two words" three)` returns `1`.

```irc
assign test blah hmm foo bar
eval echo $word($findw(hmm $test) $test)
```

This prints `hmm`, because its position is `1`.

## Source

Registration and `function_findw()`: [source/functions.c](../../source/functions.c). Function word parsing: [include/words.h](../../include/words.h). Case-insensitive UTF-8 comparison: [source/ircaux.c](../../source/ircaux.c). Locale-based uppercase mapping: [source/recode.c](../../source/recode.c).
