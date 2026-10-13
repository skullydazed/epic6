# restw function

## Synopsis

`$restw(<count> <word list>)`

## Technical

Returns the list with its first `count` words removed, starting at the zero-based word position `count`. A count of zero retains the whole list subject to surrounding-whitespace trimming. The count is required; there is no default-count form. Missing input, a negative count, or a position beyond the available words returns the empty string.

Words use normal function word parsing, with double-quoted grouping when [/xdebug dword](../commands/xdebug.md) is enabled. The result is a substring of the list: leading and trailing whitespace is trimmed, while internal spacing and the original quotes of retained words remain. It is not reconstructed into single-spaced words.

## Practical

Use this function to remove a known number of initial words when the total input length varies. [$leftw()](leftw.md) selects initial words, while [$rest()](rest.md) removes initial Unicode code points instead.

## Returns

The retained suffix of the word list. The function returns a new string, does not modify an input variable, and requires no IRC connection.

## Restrictions

Supply an integer count within the C `int` range. Normal integer parsing accepts base prefixes; nonnumeric text is not separately rejected and can convert to zero. The extraction helper reserves `32767` as an end-of-string marker: that count selects the last word rather than removing 32767 words, and greater counts return empty. Use ordinary smaller counts rather than relying on the marker behavior.

## Examples

| Expression | Result |
| --- | --- |
| `$restw(2 hello there how are you?)` | `how are you?` |
| `$restw(-1 hello there how are you?)` | Empty string. |
| `$restw(6 hello there how are you?)` | Empty string. |
| `$restw(1 one "two three" four)` with DWORD enabled | `"two three" four` |

## History

First appeared in +2, after ircII and before EPIC.

## Source

Registration and `function_restw()`: [source/functions.c](../../source/functions.c). `real_extract()` and word movement: [source/words.c](../../source/words.c). Parsing modes and the end marker: [include/words.h](../../include/words.h). Integer parsing: [include/functions.h](../../include/functions.h).
