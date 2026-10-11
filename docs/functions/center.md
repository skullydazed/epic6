# center function

## Synopsis

```text
$center(width text)
```

## Technical

`center()` returns `text` padded on the left so it appears centered within the supplied width. The width is required; there is no screen-width default. For ordinary text, the number of added spaces is `(width - display-columns(text)) / 2`, rounded down. No spaces are added on the right.

Width is converted with `strtoimax()` using base detection, accepting decimal, hexadecimal, and leading-zero octal notation. Nonnumeric input converts to zero; a numeric prefix can be accepted with trailing nonnumeric text. Missing input returns the empty string. The remaining arguments are treated as text, rather than a single word; the function itself does not remove surrounding quotes.

The measurement uses normalized display text and UTF-8 column widths, so a wide character can occupy two columns and combining characters can occupy none. It is not a byte-length measurement. If the text is wider than the requested field, it is returned unchanged, without truncation.

## Practical

Use a nonnegative width and plain text for reliable centering. A missing text argument is treated as empty text and produces `width / 2` spaces. Width zero leaves nonempty ordinary text unchanged.

For padding on both sides, use an expansion width qualifier such as `$[15]center(15 hello there)`. For direct formatting to a specified width, including center justification and fill characters, see [`fix_width()`](fix_width.md).

## Returns and restrictions

The result normally consists of leading spaces followed by the original text, with no output to the screen. It is not necessarily a string exactly `width` columns wide, because the right side is unpadded and odd spare widths round down on the left.

Formatting controls are a limitation: the implementation sizes its result from a normalized copy with attributes stripped, then formats the original argument into that buffer. Color or style sequences can therefore reduce the expected padding or cause the original text to be truncated. Use plain text, or `fix_width()`, rather than assuming formatting sequences will be preserved correctly by this function.

Negative widths are converted to an unsigned size and are not supported safely. Extremely large widths can exceed allocation or formatting limits; use a practical field width.

## Examples

| Expression | Return value |
| --- | --- |
| `$center(15 hello there)` | `  hello there` (two leading spaces) |
| `$[15]center(15 hello there)` | `  hello there  ` |
| `$center(8 abc)` | `  abc` (two leading spaces) |
| `$center(2 hello)` | `hello` |
| `$center(6 界)` | `  界` when that character occupies two columns |
| `$center()` | Empty string |

## Source

[source/functions.c](../../source/functions.c) registers `center` and implements `function_center()`. [include/functions.h](../../include/functions.h) defines width parsing. [source/screen.c](../../source/screen.c) implements normalization and display-column counting; [source/wcwidth.c](../../source/wcwidth.c) supplies Unicode width handling. [source/expr.c](../../source/expr.c) implements expansion width qualifiers.
