# fix_width function

## Synopsis

`$fix_width(<width> <left|center|right> <fill character> [<text>])`

## Technical

Formats text to a requested number of display columns, padding short text or truncating long text according to its justification. For ordinary one-column text, the result occupies exactly the requested width. This differs from [$center()](center.md), which only adds leading spaces and does not truncate.

Width is required and converted with `strtoimax()` using base detection, accepting decimal, hexadecimal, and leading-zero octal notation. Nonnumeric input converts to zero; numeric prefixes with trailing nonnumeric text can be accepted. Negative widths return the empty string.

Justification is selected by its first character, case-insensitively: `l`, `c`, or `r`. The fill argument always recognizes double-quoted words, so use `" "` for a space, independently of `/xdebug dword`. Only its first decoded Unicode code point is used. If that code point does not occupy exactly one display column, a space is used instead, including for an empty fill argument.

All text remaining after the fill argument is formatted; the function does not remove surrounding quotes from that text. Missing text is treated as empty and padded.

## Options

| Justification | Short text | Long text |
| --- | --- | --- |
| `left` | Add fill characters on the right. | Remove columns from the right. |
| `center` | Add fill characters on both sides. | Remove columns from both sides. |
| `right` | Add fill characters on the left. | Remove columns from the left. |

For centering, an odd extra padding column goes on the right; an odd extra chopped column is removed from the right.

## Returns and restrictions

Returns the formatted text without displaying it. Missing required arguments, negative width, or an unrecognized justification returns the empty string.

Measurement uses normalized display text and Unicode column widths, rather than byte length. Formatting controls are interpreted through EPIC's display normalization and current display mangling, then converted back to display text. The result is therefore not necessarily an unchanged slice of the original bytes.

Truncation at a two-column character boundary is unreliable in the current implementation. Removing only part of such a character's width can leave the result too wide, or left truncation can discard more text than intended. Prefer text with one-column characters when exact width and reliable truncation matter. Combining characters also depend on EPIC's width handling and the terminal's rendering. Use practical field widths; extreme values can exceed integer or allocation limits.

## Examples

| Expression | Result |
| --- | --- |
| `$fix_width(6 left . abc)` | `abc...` |
| `$fix_width(6 right . abc)` | `...abc` |
| `$fix_width(6 center . abc)` | `.abc..` |
| `$fix_width(3 left . abcdef)` | `abc` |
| `$fix_width(3 right . abcdef)` | `def` |
| `$fix_width(3 center . abcdef)` | `bcd` |
| `$fix_width(4 left " " x)` | `x` followed by three spaces. |
| `$fix_width(3 left .)` | `...` |

## Source

Registration and argument handling: [source/functions.c](../../source/functions.c). Padding and alignment: [source/ircaux.c](../../source/ircaux.c), `fix_string_width()`. Normalization, column counting, and truncation: [source/screen.c](../../source/screen.c). Unicode column widths: [source/wcwidth.c](../../source/wcwidth.c).
