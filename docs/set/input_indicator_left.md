# set input_indicator_left

## Synopsis

```text
/set input_indicator_left [<text>]
/set -input_indicator_left
```

Default: `+ ` (with a trailing space). With no value, [set](../commands/set.md) displays the current value. The hyphen form clears the string.

## Description

Sets the marker shown after the input prompt when the input line has scrolled horizontally away from its beginning. It is omitted in the first input zone.

On input refresh, EPIC expands dollar expressions in this string with empty arguments, normalizes its display formatting, and measures its width. A changed result causes the input line to be redrawn. Ordinary command expansion can also occur at assignment time. Unsetting hides this marker. See [input_indicator_right](input_indicator_right.md) and [input_prompt](input_prompt.md).

## Example

```text
/set input_indicator_left <
```

## Source references

- [source/input.c](../../source/input.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
