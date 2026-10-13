# revw function

## Synopsis

`$revw(<word list>)`

## Technical

Returns a copy of the list with its words in reverse order: the last word becomes first, the next-to-last becomes second, and so on. Characters within each word retain their order. [$reverse()](reverse.md) reverses Unicode code points instead.

Word extraction uses [/xdebug dword](../commands/xdebug.md). With it enabled, double quotes group words containing spaces; extracted contents are dequoted and then quoted again when they contain literal spaces, with embedded quotes escaped. Original quote spelling is not preserved. With DWORD disabled, quotes do not group words and output is appended without automatic grouping quotes.

Extracted nonempty items are joined with single spaces, and empty quoted items are omitted. The function cuts words from the end using `last_arg()` rather than uniformly trimming the entire input first; trailing whitespace can remain attached to the first returned item. Do not rely on it to normalize all surrounding whitespace. It returns a new string and does not modify a variable supplied as input.

## Practical

Use this function to reverse the order of entries in a word list without reversing their individual text.

## Returns

The reversed word list. Empty input returns the empty string. There are no options or function-specific error diagnostics, and no IRC connection is required.

## Examples

| Expression | Result |
| --- | --- |
| `$revw(welcome to our channel)` | `channel our to welcome` |
| `$revw(one "two three" four)` with DWORD enabled | `four "two three" one` |
| `$revw(one "" two)` with DWORD enabled | `two one` |
| `$revw(one one two)` | `two one one` |

## History

First appeared in +6, after ircII and before EPIC.

## Source

Registration and `function_revw()`: [source/functions.c](../../source/functions.c). `last_arg()`, dequoting, and `malloc_strcat_word()`: [source/ircaux.c](../../source/ircaux.c). Backward word movement: [source/words.c](../../source/words.c).
