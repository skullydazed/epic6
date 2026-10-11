# chngw function

## Synopsis

```text
$chngw(index replacement word-list)
```

## Technical

`chngw()` returns `word-list` with the word at `index` replaced by `replacement`. Indexing starts at zero. It returns a new string; it does not update a variable unless the caller assigns the result.

A missing index or replacement returns the empty string. A negative index returns the remaining word list unchanged. A nonnegative index beyond the end appends the replacement rather than adding empty words to reach that position. If the word list is missing or empty, a nonnegative index returns the replacement.

Index conversion uses `strtoimax()` with base detection before storing it as an `int`: decimal, hexadecimal, and leading-zero octal notation are accepted, as is a numeric prefix with trailing nonnumeric text. Nonnumeric input converts to zero. Use indices within the platform's `int` range.

Replacement parsing and list extraction honor [`xdebug dword`](../commands/xdebug.md). With that flag enabled, a double-quoted phrase counts as one word, the replacement's surrounding quotes are removed for parsing, and a replacement containing spaces is quoted again in the returned list. With it disabled, whitespace separates words and quotes are ordinary characters. An empty quoted replacement in `dword` mode deletes the selected word rather than inserting an empty placeholder.

## Practical

Use this function to change an entry in a word list, such as an access level or a named list element, then assign the returned list back to the desired variable.

The result is assembled from the slices before and after the selected word, with separators around the replacement. Internal spacing within those slices can be retained, while surrounding whitespace can be trimmed or changed. Do not rely on exact leading or trailing whitespace. See [`insertw()`](insertw.md) to insert without replacing and [`delw()`](delw.md) to remove an indexed word.

## Returns and examples

| Expression | Return value |
| --- | --- |
| `$chngw(2 blah foo bar booya)` | `foo bar blah` |
| `$chngw(0 new old second)` | `new second` |
| `$chngw(-1 new old second)` | `old second` |
| `$chngw(8 new old second)` | `old second new` |
| `$chngw(0 new)` | `new` |
| `$chngw()` | Empty string |

With `xdebug dword` enabled, `$chngw(1 "two words" one old three)` returns `one "two words" three`.

## History

This function first appeared in “plus-2” (post-ircII, pre-EPIC).

## Source

[source/functions.c](../../source/functions.c) registers `chngw` and implements `function_chngw()`. [include/functions.h](../../include/functions.h) defines index and argument parsing; [include/words.h](../../include/words.h) and [source/words.c](../../source/words.c) implement word extraction. [source/ircaux.c](../../source/ircaux.c) implements optional replacement quoting and list concatenation.
