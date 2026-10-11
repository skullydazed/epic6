# $curpos()

## Synopsis

`$curpos()`

## Technical

Returns the zero-based logical cursor position in the input line belonging to the current window's screen. Counting starts at the beginning of the editable input, after the prompt, and does not change when the input line scrolls horizontally. Arguments, if any, are ignored; this function does not move the cursor.

The position counts EPIC input tokens, not UTF-8 bytes or terminal columns. A wide character counts as one token even if it occupies two columns. Zero-width code points, such as combining marks, are skipped by the input tokenizer rather than counted separately. Control characters whose width is considered invalid count as one token. This is not a general Unicode grapheme-cluster index.

## Practical

Use this function with [`$indextoword()`](indextoword.md) to help find the word under the cursor, for example in a completion key binding. For ASCII input, its index also works with [`$mid()`](mid.md). Combining or other zero-width characters can make the logical cursor index differ from the code-point index used by string functions.

The selected input line comes from the current window's screen, while `$L` reads the last-input screen's buffer. They normally agree in a key binding, but can refer to different screens in other execution contexts. There is no argument for selecting a screen or window; [`/xeval -window`](../commands/xeval.md) can temporarily change the current window for the query.

## Returns

Returns a decimal integer logical position, with `0` at the beginning and the token count at the end of the input. Returns `-1` if the current window has no screen input line, such as a hidden window.

## Examples

In a key binding on the same screen, with plain ASCII input:

```irc
@ position = curpos()
@ character = mid($position 1 $L)
```

If the input is `hello` and the cursor is just before `l`, `position` is `2` and `character` is `l`. At the end of the input, the one-character extraction is empty. Check for `-1` when the execution context can use a window without a screen.

## History

This function first appeared in ircII-2.2.2.

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). Screen selection is in [source/window.c](../../source/window.c); cursor state, tokenization, and `$L`'s input buffer access are in [source/input.c](../../source/input.c). Screen input-line lookup is in [source/screen.c](../../source/screen.c).
