# wordtoindex

Convert a zero-based word position to an offset in list text.

## Synopsis

```irc
$wordtoindex(<number> <list>)
```

## Technical

The number counts words from zero. A missing number returns an empty string. Zero or a negative number returns `0`; a number that advances beyond all words returns the length of the remaining list text, as counted by [strlen](strlen.md). An omitted list therefore produces `0`.

Only one separating whitespace character after the number is consumed by its argument parser; additional whitespace belongs to the list and contributes to the offset. Word traversal honors double-quoted grouping when `/xdebug dword` is enabled. Quotes remain part of the text being counted.

For ordinarily single-spaced lists, the result is the offset of the first character of the selected word. There is a spacing quirk: each traversal skips a word and consumes only one following whitespace character. With repeated separators, the returned position can point into the remaining whitespace before the selected word. Index zero always points at the start of the list, including any leading whitespace.

For valid UTF-8, offsets count code points, rather than bytes, grapheme clusters, or terminal columns. The counter counts ASCII bytes and bytes at least `0xc0`, skipping continuation bytes; it does not validate UTF-8. Formatting controls and quotes count too. Number parsing is permissive base-zero integer conversion followed by narrowing to C `int`; use ordinary decimal word positions rather than relying on malformed or oversized numbers.

## Practical

This function can locate a word after text has been rewritten, for example in completion scripts that also use [indextoword](indextoword.md). Take account of repeated spaces when using the result to edit text. It is not a general inverse of that function for all spacing and positions.

[Curpos](curpos.md) uses input cursor positions with its own screen and character-width limitations; do not assume every code-point offset from this function is interchangeable with a displayed cursor column.

## Returns

A decimal offset into the list text, or an empty string when the number is missing. The function does not modify the list or a variable.

## Examples

```irc
$wordtoindex(2 foo bar blah)   /* 8 */
$wordtoindex(999 foo bar blah) /* 12 */
$wordtoindex(-1 foo bar blah)  /* 0 */
$wordtoindex(1 foo  bar)       /* 4: the second space, before bar. */
$wordtoindex(1 é x)           /* 2, counting the é as one code point. */
```

## Source

Registered and implemented as `function_wordtoindex` in [source/functions.c](../../source/functions.c). Word traversal uses `real_move_to_abs_word` and `move_to_next_word` in [source/words.c](../../source/words.c). Offsets use `quick_code_point_index` in [source/wcwidth.c](../../source/wcwidth.c).
