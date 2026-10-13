# $sort()

## Synopsis

`$sort(<list>)`

## Technical

Sorts the supplied word list in ascending case-insensitive order. Comparison decodes UTF-8 codepoints and applies EPIC's uppercase mapping. This is not locale-aware dictionary collation or a server-specific nickname comparison, and there is no Unicode normalization. Uppercase words do not form a separate group ahead of lowercase words.

Words are compared lexically, not numerically: `10` sorts before `2`. [$numsort()](numsort.md) provides comparison of integer portions within words. [$sortby()](sortby.md) accepts a custom comparison.

Word grouping honors [/xdebug dword](../commands/xdebug.md). With it enabled, a double-quoted phrase is one sortable word. The result is rebuilt with single spaces between nonempty words, and words containing literal spaces are quoted again with embedded double quotes escaped. Original separator spacing and quote spelling are not preserved. With DWORD disabled, quotes do not group words.

## Practical

Use this to alphabetize a list for display or later processing. It returns a new list without changing the source variable. Repeated nonempty words are retained; it does not remove duplicates.

Words that compare equal, including spellings differing only in case, have no guaranteed relative order because the implementation uses an unstable sort. Malformed UTF-8 is not validated by this function and should not be used to infer a meaningful ordering.

## Returns

The sorted list, or empty for an empty list. Empty quoted words are omitted when rebuilding the result. There are no options, separate error codes, or IRC connection requirements.

## Examples

| Expression | Result |
| --- | --- |
| `$sort(foo bar blah erf booya)` | `bar blah booya erf foo` |
| `$sort(foo Bar blah Erf booya)` | `Bar blah booya Erf foo` |
| `$sort(2 10 1)` | `1 10 2` |
| `$sort(b a a)` | `a a b` |
| `$sort()` | Empty string |

With `/xdebug dword` enabled, `$sort("two words" apple)` returns `apple "two words"`.

Registration, `function_sort()`, and `sort_it()`: [source/functions.c](../../source/functions.c). Word splitting, result reconstruction, and Unicode comparison: [source/ircaux.c](../../source/ircaux.c).
