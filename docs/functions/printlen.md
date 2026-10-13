# printlen function

## Synopsis

`$printlen(<text>)`

## Technical

Returns the display-column width of the text after EPIC's normalization, not its byte length or number of characters. The entire argument string is measured without word splitting or quote removal. Empty input returns `0`.

IRC formatting attributes and color parameters do not contribute width. Recognized ANSI escape sequences are parsed during normalization rather than counted as literal printable text; there is no need to strip ordinary ANSI color sequences first. Cursor-right escape sequences can contribute non-destructive spaces. Handling of malformed or unsupported sequences follows EPIC's escape parser.

Unicode width uses EPIC's code-point width tables: combining characters can contribute zero columns, and wide characters can contribute two. This is a sum of individual code-point widths, not a measurement of a terminal's rendering of complex grapheme or emoji sequences.

Tabs are expanded to the next eight-column stop in the normalized text. Newlines contribute zero columns without resetting the accumulated width or the tab position, so multiline text is measured as a total rather than the widest line. Bells contribute no width and do not sound during measurement. Other controls follow normalization rules: some are removed, while others become printable control representations.

## Practical

Use this function to format columns containing highlight attributes or color codes. [$pad()](pad.md) and [$fix_width()](fix_width.md) provide related display-width formatting operations. [$numlines()](numlines.md) measures line wrapping instead of total columns.

The function uses its own fixed normalization mode, not `display_line_mangler`, output rewriting, line wrapping, or continuation markers. Its result therefore need not equal the width of text after the complete window output pipeline. It produces no screen output and does not change its input variable.

## Returns

The measured width as a decimal integer string. There are no options or function-specific error returns. No IRC connection is required.

## Examples

| Expression | Result |
| --- | --- |
| `$printlen(hello world)` | `11` |
| `$printlen(界)` | `2` |
| `$printlen()` | `0` |

The text `blah`, followed by an IRC color control with parameters `4,2`, followed by `foo`, an IRC bold control, and `bar`, has width `10`; the formatting controls and color parameters add no columns.

## Source

Registration and `function_printlen()`: [source/functions.c](../../source/functions.c). `new_normalize_string()` and the non-output mode of `output_with_count()`: [source/screen.c](../../source/screen.c). Unicode width tables: [source/wcwidth.c](../../source/wcwidth.c).
