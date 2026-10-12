# insertw function

## Synopsis

`$insertw(<index> <word> [<word list>])`

## Technical

Returns a copy of the word list with `word` inserted at the zero-based word index. No existing word is removed. A positive index beyond the list's end appends the word without creating empty intervening words.

If index is less than one, the function returns all remaining input exactly as supplied, without separately parsing the inserted word. Thus zero or any negative index places the already-present word at the front and preserves its quotes and separator spaces. Negative indexes do not count backward from the end.

For positive indexes, both the inserted word and word-list boundaries follow `/xdebug dword`. With it enabled, a double-quoted group is one word; a multiword inserted value is quoted again in the result to preserve grouping. With it disabled, the next ordinary word is inserted and the remainder becomes the list. Unlike [$insert()](insert.md), this function does not always enable quoted-word parsing.

Index uses base-detecting integer conversion: decimal, hexadecimal, and leading-zero octal are accepted; nonnumeric text converts to zero. Missing index returns empty. For a positive index, a missing inserted word also returns empty, while an omitted list returns just the inserted word. An explicitly empty inserted value contributes no word.

For positive indexes, prefix and suffix are extracted as word ranges and joined with spaces around the inserted value. Leading/trailing whitespace may be trimmed, while whitespace within extracted ranges can remain; do not rely on exact separator preservation. The entire remaining input after the inserted word is the list.

## Practical

Use this to insert a word at a known list position. At index zero, the result is simply the remaining arguments, resembling the list produced by [$unshift()](unshift.md); this function returns text without assigning a variable. [$chngw()](chngw.md) replaces an existing word instead.

## Returns

The resulting word list, or empty for missing required arguments. No error or diagnostic is returned for a negative or beyond-end index.

## Examples

| Expression | Result |
| --- | --- |
| `$insertw(1 blah hi there bob)` | `hi blah there bob` |
| `$insertw(3 foo now is the time for)` | `now is the foo time for` |
| `$insertw(0 blah hi there)` | `blah hi there` |
| `$insertw(-1 blah hi there)` | `blah hi there` |
| `$insertw(100 blah hi there)` | `hi there blah` |
| `$insertw(1 blah)` | `blah` |

## History

First appeared in “plus-2” (post-ircII, pre-EPIC).

## Source

Registration, front-insertion shortcut, extraction, and concatenation: [source/functions.c](../../source/functions.c). Word-range extraction: [source/words.c](../../source/words.c). Quote-aware output and integer conversion: [source/ircaux.c](../../source/ircaux.c). Function word-mode macros: [include/words.h](../../include/words.h).
