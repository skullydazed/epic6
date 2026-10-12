# $midw()

## Synopsis

`$midw(<start> <length> <word list>)`

## Technical

Returns up to `length` words after skipping the first `start` words. Positions count from zero. For nonnegative starts, this selects words `start` through `start + length - 1`, inclusive. If fewer words remain than requested, all remaining words are returned without padding.

Missing start or length, a length less than one, an empty list, or a start beyond the available words returns the empty string. Start and length use base-detecting integer conversion, including hexadecimal and leading-zero octal; nonnumeric text converts to zero. Both values are stored as C `int` values. The entire input after those arguments is the word list.

Word boundaries honor [/xdebug dword](../commands/xdebug.md). With it enabled, a double-quoted group is one word; with it disabled, whitespace separates words even inside quotes. The result is extracted text, not a rebuilt list: quotation marks and spacing between selected words are preserved. Leading and trailing whitespace are subject to trimming.

A negative start does not count backward from the end. For ordinary negative values, the helper begins at word zero but still calculates the last selected word as `start + length - 1`. If that last position is negative, the result is empty; otherwise the prefix through that position is returned. Prefer nonnegative starts for predictable ranges.

## Practical

Use this when you want words from inside a word list. [$leftw()](leftw.md) and [$rightw()](rightw.md) select words from either end. [$mid()](mid.md) extracts by Unicode code-point position instead.

## Returns

The selected words, or the empty string under the conditions above. The source variable is not changed.

## Restrictions

Keep both arguments and `start + length - 1` within the C `int` range. The extraction helper reserves `32767` as an end-of-string marker: using it as a start selects the last word, while using it as the calculated end selects the remainder. Avoid that boundary when requesting an ordinary numeric range.

## Examples

| Expression | Result |
| --- | --- |
| `$midw(2 2 hello there how are you?)` | `how are` |
| `$midw(-1 3 hello there bob)` | `hello there` |
| `$midw(-3 2 hello there bob)` | Empty string |
| `$midw(0 7 hello there bob)` | `hello there bob` |
| `$midw(1 2 hello there   bob)` | `there   bob` |
| `$midw(1 0 hello there bob)` | Empty string |

With `xdebug dword` enabled, `$midw(1 1 one "two words" three)` returns `"two words"`, including the quotation marks.

## History

This function first appeared in “plus-2” (post-ircII, pre-EPIC).

Registration and `function_midw()`: [source/functions.c](../../source/functions.c). `extractfw` and the end marker: [include/words.h](../../include/words.h). The `real_extract()` helper: [source/words.c](../../source/words.c). Integer argument conversion: [include/functions.h](../../include/functions.h).
