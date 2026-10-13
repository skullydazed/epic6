# remws function

## Synopsis

`$remws(<words to remove> / <word list>)`

## Technical

Returns the words on the right that do not also occur on the left. Comparisons are literal whole-word comparisons, case-insensitive using EPIC's Unicode comparison. They are not wildcard or regular-expression matches. All matching occurrences on the right are removed; duplicates on the left do not limit how many are removed.

The first literal slash in the input separates the lists, even if it occurs inside quotes. The removal list therefore cannot contain a slash; later slashes may occur in right-hand words. Missing slash returns the empty string.

Both lists use word-list parsing with double-quoted grouping when [/xdebug dword](../commands/xdebug.md) is enabled. Input words are dequoted for comparison. The retained words preserve their order, spelling, and duplicates, but separators are rebuilt as single spaces. With DWORD enabled, returned words containing literal spaces are quoted again and embedded double quotes are escaped. Empty words are omitted from the result. Original quote spelling is not preserved.

The left list is sorted for lookup; the right list is not sorted. The function returns a new list and does not modify a variable whose expanded value was supplied.

## Practical

Use this function to remove multiple known words from a list. [$remw()](remw.md) removes only the first occurrence of one word. [$filter()](filter.md) instead removes words matching a wildcard pattern.

## Returns

The right-hand list with every word found in the removal list omitted. An empty removal list retains all right-hand words while rebuilding their separators and quoting. An empty right list, removal of every word, or missing separator returns empty. There are no options or function-specific error diagnostics, and no IRC connection is required.

## Examples

| Expression | Result |
| --- | --- |
| `$remws(foo bar / test blah foo fnord bar nyah)` | `test blah fnord nyah` |
| `$remws(foo / FOO bar foo bar)` | `bar bar` |
| `$remws(/ one  two)` | `one two` |
| `$remws("two three" / one "two three" four)` with DWORD enabled | `one four` |

## Source

Registration, `function_remws()`, and the case-insensitive `sort_it()` comparator: [source/functions.c](../../source/functions.c). Word-list splitting, Unicode comparison, and result quoting: [source/ircaux.c](../../source/ircaux.c).
