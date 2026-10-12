# indextoword function

## Synopsis

`$indextoword(<position> <text>)`

## Technical

Converts a zero-based character position in `text` to a zero-based word number suitable for [$word()](word.md) and other word functions. For valid UTF-8, positions count Unicode code points rather than bytes or display columns, as do [$index()](index.md) and [$rindex()](rindex.md).

Position is the first function argument word, converted as an integer with base detection. Nonnumeric text converts to zero. Text is the entire remaining input; additional separator spaces and quotation marks are part of it. Word counting follows `/xdebug dword`: double-quoted groups count as one word when enabled, otherwise whitespace separates words normally.

For ordinary unquoted words, the first space after a word belongs to that preceding word; the second and subsequent spaces belong to the following word. Leading spaces map to word zero. A position exactly at the end returns the last word number, including when the text has trailing spaces. Empty text with position zero returns `-1`.

Missing position, a negative position, or a position beyond the text's code-point length returns empty. There is no negative-position convention for counting backward from the end. Malformed UTF-8 can make the quick length check and decoding disagree; use valid UTF-8 for reliable positions.

## Practical

Find the word under the input cursor using `$L` (the input line) and [$curpos()](curpos.md):

```irc
$word($indextoword($curpos() $L) $L)
```

This is useful for completion scripts with ASCII input on the same screen. `curpos()` uses logical input tokens, which can differ from code-point positions when zero-width characters occur, and `$L` can refer to a different screen in other execution contexts. At the end of a nonempty line it selects the last word, rather than a new empty word after a trailing separator.

## Returns

A zero-based word number, `-1` for empty text at position zero, or empty for missing/invalid-range position. No diagnostic is printed.

## Examples

```irc
@ foo = [one two. three.]
```

| Expression | Result |
| --- | --- |
| `$indextoword($index(. $foo) $foo)` | `1` |
| `$indextoword($rindex(. $foo) $foo)` | `2` |
| `$indextoword(3 one  two)` | `0` (first separator space) |
| `$indextoword(4 one  two)` | `1` (second separator space) |
| `$indextoword(7 one two)` | `1` (end position) |
| `$indextoword(100 testing)` | Empty string |
| `$indextoword(0)` | `-1` |

## Source

Registration, position validation, text-prefix counting, and end handling: [source/functions.c](../../source/functions.c). Word counting and quote mode: [source/words.c](../../source/words.c). Code-point length and decoding: [source/wcwidth.c](../../source/wcwidth.c). Integer conversion: [include/functions.h](../../include/functions.h).
