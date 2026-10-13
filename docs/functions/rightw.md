# rightw function

## Synopsis

`$rightw(<count> <word list>)`

## Technical

Returns the last `count` words from the list. The count is required and uses normal integer argument parsing. Missing input, a count less than one, or an empty list returns the empty string. A count exceeding the available words returns the whole list.

Double quotes group words when [/xdebug dword](../commands/xdebug.md) is enabled. The retained suffix is copied from the original text, preserving its internal separators and quote spelling rather than rebuilding or dequoting the words. Characters within words retain their order.

This uses the whitespace-preserving extraction helper. Trailing whitespace remains in the result, and selection from the start can retain leading whitespace. It does not uniformly trim the list despite comments in the implementation suggesting otherwise.

## Practical

Use this function to select the last few words from a list. [$leftw()](leftw.md) selects initial words, [$restw()](restw.md) removes a number of initial words, and [$right()](right.md) selects trailing Unicode code points instead.

## Returns

The selected suffix as a new string. It does not change an input variable, produce normal screen output, or require an IRC connection.

## Restrictions

Supply a positive integer within the C `int` range; oversized conversions are not safely range-checked. The extraction helper's reserved start marker means a count of `32767` selects the whole input directly rather than walking back that many words. Normal smaller counts need no knowledge of the marker.

## Examples

| Expression | Result |
| --- | --- |
| `$rightw(2 hello there how are you?)` | `are you?` |
| `$rightw(-1 hello there bob)` | Empty string. |
| `$rightw(7 hello there bob)` | `hello there bob` |
| `$rightw(1 one "two three")` with DWORD enabled | `"two three"` |

## History

First appeared in plus-2, after ircII and before EPIC.

## Source

Registration and `function_rightw()`: [source/functions.c](../../source/functions.c). `real_extract2()` and backward word movement: [source/words.c](../../source/words.c). Parsing modes and reserved markers: [include/words.h](../../include/words.h). Integer parsing: [include/functions.h](../../include/functions.h).
