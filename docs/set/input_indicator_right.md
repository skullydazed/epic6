# set input_indicator_right

## Synopsis

```text
/set input_indicator_right [<text>]
/set -input_indicator_right
```

Default: ` + ` (with leading and trailing spaces). With no value, [set](../commands/set.md) displays the current value. The hyphen form clears the string.

## Description

Sets the marker at the right edge of the input display when more input remains beyond the visible zone. It is omitted in the final input zone.

On input refresh, EPIC expands dollar expressions in this string with empty arguments, normalizes its display formatting, and measures its width. A changed result causes the input line to be redrawn. Ordinary command expansion can also occur at assignment time. Unsetting hides this marker. See [input_indicator_left](input_indicator_left.md) and [input_prompt](input_prompt.md).

## Example

```text
/set input_indicator_right >
```

## Source references

- [source/input.c](../../source/input.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
