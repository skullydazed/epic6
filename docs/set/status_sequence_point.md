# set status_sequence_point

## Synopsis

```text
/set status_sequence_point [<text>]
/set -status_sequence_point
```

Default: ` {{{%P}}}` (with a leading space). With no value, [set](../commands/set.md) displays the current value. The hyphen form clears the string.

## Description

Formats the internal sequence-point counter for `%{2}P` in [status_format](status_format.md). This field is empty unless `SEQUENCE_POINTS` is enabled through [xdebug](../commands/xdebug.md). It is a diagnostic counter advanced by EPIC’s I/O loop.

Inside this string, the first `%P` is replaced with the decimal counter. This differs from the outer status field `%{2}P`; additional `%P` occurrences are literal. Changing the string rebuilds status formats. Unsetting it hides the field.

## Example

```text
/set status_sequence_point [seq=%P]
```

## Source references

- [source/status.c](../../source/status.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
