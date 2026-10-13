# $rsubstr()

## Synopsis

`$rsubstr(<substring> <search string>)`

## Technical

Intended to search from the end of the search string and return the zero-based position of the last occurrence of the substring. Its comparison uses Unicode-aware case-insensitive matching, and positions count UTF-8 codepoints from the beginning, rather than bytes or display columns. It searches literal text, not wildcard patterns.

The substring is a double-quoted word: double quotes can group a substring containing spaces regardless of `/xdebug dword`. The entire remaining text is the search string, including spaces and literal quotation marks; do not quote it merely to group words.

**The current implementation does not perform a working reverse search.** For valid, nonempty UTF-8 arguments whose substring is no longer than the search string, the search loop fails to advance its position and does not terminate. Such a call can block the client. This defect also affects calls where the substring is absent; it is not limited to successful matches.

## Practical

A reverse substring search would be useful for locating specific text in server output or finding the last separator in a string. Until the implementation is corrected, use [$substr()](substr.md) when a forward search is sufficient. [$rindex()](rindex.md) searches backward for a member of a character set and has different semantics.

## Returns

Missing substring input returns the empty string. An empty substring, empty search string, or substring longer than the search string returns `-1`.

The intended remaining results are `-1` for no occurrence or a nonnegative codepoint index for the last occurrence. The current blocking defect prevents relying on these results for ordinary nonempty searches. Invalid UTF-8 is not validated by this function and is outside those guarantees.

## Examples

| Expression | Current result |
| --- | --- |
| `$rsubstr()` | Empty string |
| `$rsubstr(abc)` | `-1` (empty search string) |
| `$rsubstr(long x)` | `-1` (substring longer than search string) |
| `$rsubstr("" abc)` | `-1` (empty substring) |

Avoid testing ordinary searches in a running client until the blocking defect is corrected.

## History

First appeared in EPIC4pre1.016.

## Source

Registration and `function_rsubstr()`: [source/functions.c](../../source/functions.c). `rstristr()` and case-insensitive comparison: [source/ircaux.c](../../source/ircaux.c). Backward decoding and codepoint positions: [source/wcwidth.c](../../source/wcwidth.c). Double-quoted parsing: [include/functions.h](../../include/functions.h).
