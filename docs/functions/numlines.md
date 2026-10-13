# $numlines()

## Synopsis

`$numlines(<columns> <string>)`

## Technical

Returns the number of lines EPIC's display-preparation helper produces for the supplied text at the requested width. It normalizes formatting and uses the same wrapping machinery as displayed output. This is not simply the character count divided by the width.

The width is the first integer function argument, using base-detecting conversion, including hexadecimal and leading-zero octal. Nonnumeric text converts to zero. A missing width or a value less than one returns the empty string. The width is stored as a C `int`; keep it within that range. The entire remaining input is the text, including spaces, extra words, and literal quotation marks; it is not separately dequoted.

The wrapper subtracts one from `columns` before calling the helper. That helper measures terminal columns for Unicode text, handles formatting attributes and explicit newlines, and uses the global `first_line`, `continued_line`, `indent`, and `word_break` settings. The continued-line marker is also normalized using the display mangling settings. This call supplies no window reference, so it uses the global indent setting rather than a particular window's indent setting.

## Practical

This can estimate how much screen space text needs, for example in a script pager. Use the intended display width and the same relevant display settings as the eventual output. [$geom()](geom.md) supplies screen geometry, while [$printlen()](printlen.md) measures display width without counting wrapped lines.

## Returns

The number of prepared lines, or the empty string for a missing or nonpositive width. A valid width with empty text returns at least one prepared line. The call does not display the text, add it to scrollback, or change the source variable.

## Restrictions

The calculation does not apply `output_rewrite`, output hooks, or later changes to text before display. Its initial normalization uses the built-in normalization mode rather than reproducing every output-mangling choice for the main text. A count for one width or set of global settings need not match a window with different settings.

The display helper has special handling near the end of a line and for continuation markers. Treat the result as EPIC's current wrapping calculation, rather than a guarantee that every resulting line fits a strict column bound. Malformed UTF-8 and control characters are handled by the normalizer and display helper rather than counted as literal bytes.

## Examples

With an empty `first_line`, `$numlines(80 foo bar baz)` returns `1`. With an empty first-line and continuation marker and `indent` off, `$numlines(6 foo bar baz)` returns `2` under the current helper's wrapping behavior.

```text
$numlines($word(0 $geom()) foo bar baz)    count using the reported screen width
$numlines(foo bar baz)                    empty string (width converts to zero)
$numlines(0 text)                         empty string
$numlines(80)                             1 with an empty first_line
```

Registration and `function_numlines()`: [source/functions.c](../../source/functions.c). Normalization, display-column measurement, settings, and wrapping: [source/screen.c](../../source/screen.c), `new_normalize_string()` and `prepare_display()`. Integer conversion: [include/functions.h](../../include/functions.h).
