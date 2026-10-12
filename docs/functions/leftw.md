# $leftw()

## Synopsis

`$leftw(<count> <word list>)`

## Technical

Returns the first `<count>` words from the supplied list. A missing count, a count less than `1`, or an empty list returns the empty string. If the count exceeds the number of words, all available words are returned without padding.

The count is the first integer function argument, using base-detecting conversion: decimal, hexadecimal, and leading-zero octal are accepted. Nonnumeric text converts to zero. The count is stored as a C `int`; keep it within that type's range. The entire remaining input is the word list.

Word boundaries honor [xdebug](../commands/xdebug.md) `dword`. With it enabled, a double-quoted group is one word; with it disabled, whitespace separates words even inside quotes. The result is extracted text, not a rebuilt list: quotation marks and spacing between selected words are preserved. Leading and trailing whitespace are subject to trimming.

## Practical

Use this when you need the first few words in a list. For extraction from the end, see [rightw](rightw.md); for a prefix measured in Unicode code points, see [left](left.md).

## Returns

The selected initial words, or the empty string for missing input, a nonpositive count, or no words. The source variable is not changed.

## Examples

| Expression | Result |
| --- | --- |
| `$leftw(2 hello there how are you?)` | `hello there` |
| `$leftw(-1 hello there bob)` | Empty string |
| `$leftw(7 hello there bob)` | `hello there bob` |
| `$leftw(2 hello   there bob)` | `hello   there` |
| `$leftw(1 "hello there" bob)` | `"hello there"` with `xdebug dword` enabled |
| `$rightw(2 hello there how are you?)` | `are you?` |

## History

This function first appeared in “plus-2” (post-ircII, pre-EPIC).

## Source

Registered and implemented as `function_leftw` in [source/functions.c](../../source/functions.c). `extractfw` is defined in [include/words.h](../../include/words.h), and its `real_extract` helper is in [source/words.c](../../source/words.c). Integer argument conversion is in [include/functions.h](../../include/functions.h).
