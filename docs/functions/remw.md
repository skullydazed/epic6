# remw function

## Synopsis

`$remw(<word> <word list>)`

## Technical

Returns the list with the first, leftmost occurrence of the literal word removed. Matching is case-insensitive using EPIC's Unicode comparison; it is not wildcard or substring matching. Later copies of the matching word remain.

Both the search word and list use normal function word parsing. Double quotes group words containing spaces when [/xdebug dword](../commands/xdebug.md) is enabled; comparison uses their dequoted contents. Without that mode, quoted text does not select a consecutive multiword phrase for removal.

When a match is found, retained portions are extracted with leading and trailing whitespace trimmed, then joined with one space if both portions are nonempty. Internal spacing and quote spelling within a retained portion remain. This does not simply delete one separator byte around the removed word. If no match is found, the remaining list text is returned without this reconstruction.

Missing search input or an empty list returns the empty string. The function returns a new string and does not modify a variable whose expanded value is supplied.

## Practical

Use this function when you know the word to remove but not its position. It combines the first-match lookup of [$findw()](findw.md) with the removal behavior of [$notw()](notw.md). [$remws()](remws.md) provides a related operation for removing words.

## Returns

The word list with its first matching word removed, or the unchanged remaining list when there is no match. Removing the only word returns empty. There are no options or function-specific error diagnostics, and no IRC connection is required.

## Examples

| Expression | Result |
| --- | --- |
| `$remw(two one  two  three)` | `one three` |
| `$remw(two one two three two one)` | `one three two one` |
| `$remw(TWO one two three)` | `one three` |
| `$remw(missing one  two)` | `one  two` |
| `$remw("two three" one "two three" four)` with DWORD enabled | `one four` |

## History

First appeared in +2, after ircII and before EPIC.

## Source

Registration, `function_remw()`, and `function_findw()`: [source/functions.c](../../source/functions.c). Retained-word extraction: [source/words.c](../../source/words.c). Parsing modes: [include/words.h](../../include/words.h). Case-insensitive comparison and joining: [source/ircaux.c](../../source/ircaux.c).
