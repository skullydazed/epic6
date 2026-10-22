# set first_line

## Synopsis

```text
/set first_line [<text>]
/set -first_line
```

Default: unset. With no value, [set](../commands/set.md) displays the current value. The hyphen form clears the string.

## Description

Prepends text to each logical output string processed by the display line wrapper, before it splits that string into terminal-width rows. It is not a heading printed only once at startup or at the top of a window. The prefix occupies display width and can affect wrapping.

Wrapped continuation rows use [continued_line](continued_line.md). The display wrapper copies `first_line` directly without evaluating dollar expressions; ordinary command expansion can still occur when assigning it. Unsetting removes the prefix.

## Example

```text
/set first_line >
/echo Example output
/set -first_line
```

## Source references

- [source/screen.c](../../source/screen.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
