# set status_prefix_when_not_current

## Synopsis

```text
/set status_prefix_when_not_current [<text>]
/set -status_prefix_when_not_current
```

Default: empty string. With no value, [set](../commands/set.md) displays the current value. The hyphen form clears the string.

## Description

Sets the global fallback text returned by `%{1}P` in [status_format](status_format.md) for a window other than its screen’s current input window. The name does not mean EPIC automatically prepends it to every status line: the format must contain `%{1}P`.

A window’s own `status_prefix_when_not_current` override takes precedence when present, including an explicitly empty override. Otherwise this global string is inserted directly; unsetting it supplies no text. Changing it rebuilds status formats. See [status_prefix_when_current](status_prefix_when_current.md) and [window](../commands/window.md).

## Example

```text
/set status_prefix_when_not_current -
```

## Source references

- [source/status.c](../../source/status.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
