# qword function

## Synopsis

`$qword(<index> <word list>)`

## Technical

Returns the word at the zero-based integer index in the list. Missing index or list input, a negative index, or an index beyond the last word returns the empty string.

Input grouping depends on [/xdebug dword](../commands/xdebug.md). With it enabled, double quotes group words containing spaces, and extraction removes the surrounding quotes and unescapes the quoted contents. The result is then quoted again if it contains a literal space, with embedded double quotes escaped. This reconstructs a grouped word rather than preserving its original quote spelling.

With DWORD disabled, quotes do not group input words and the result is not automatically quoted. Despite its name, the function does not force double-quoted word parsing or result quoting independently of that setting. Automatic result quoting tests literal spaces, not all whitespace characters.

## Practical

Use this function when extracting an item to pass onward as a grouped word. [$word()](word.md) instead returns the extracted word's contents without reconstructing grouping quotes. Use a consistent DWORD setting for both extraction and subsequent list parsing.

## Returns

The selected word, with result quoting as described above. An empty quoted word also returns the empty string, so an empty result does not distinguish that valid selection from missing or out-of-range input. The function does not change its input variable and requires no IRC connection.

## Examples

| Expression | Result |
| --- | --- |
| `$qword(2 foo bar blah)` | `blah` |
| `$qword(1 one "two three" four five)` with DWORD disabled | `"two` |
| `$qword(1 one "two three" four five)` with DWORD enabled | `"two three"` |
| `$qword(0 "")` with DWORD enabled | Empty string. |
| `$qword(-1 one two)` | Empty string. |

## History

First appeared in EPIC5-0.3.2.

## Source

Registration and `function_qword()`: [source/functions.c](../../source/functions.c). Function word parsing: [include/words.h](../../include/words.h). `malloc_strcat_word()` and result quoting: [source/ircaux.c](../../source/ircaux.c).
