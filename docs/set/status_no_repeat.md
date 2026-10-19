# set status_no_repeat

## Synopsis

```text
/set status_no_repeat [on|off|toggle]
```

Default: `off`. With no argument, [set](../commands/set.md) displays the current value.

## Description

When on, inhibits the normal repetition of a status line's final fill character and uses spaces for the remaining width instead. It does not shorten the status row or remove its formatting attributes.

When off, EPIC normally fills unused width by repeating the last printable character of the rendered left section. Without a `%>` right-alignment field, that is the last printable character of the entire rendered line. With `%>`, it fills the gap before the right section using the last printable character before that split, not the last character on the right. Formatting control sequences do not themselves become fill characters.

Ending the relevant section with a space produces space fill even with this setting off. A missing or nonpositive-width fill character also falls back to a space. Since filling follows status expansion and normalization, a field's rendered value can determine the chosen character.

## Effects

This global policy applies to single and double status rows, including per-window format overrides. Changing it rebuilds status formats and requests updates. It does not change the stored [status_format](status_format.md) text, ordinary window output, or input-line editing, and sends no IRC request.

## Example

Keep a compact format from repeating its final character across the remaining row:

```text
/set status_no_repeat on
```

Restore normal character fill with `/set status_no_repeat off`. To retain space fill only in a particular format, end its left section with a space instead.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration, status rebuild callback, and off default.
- [source/status.c](../../source/status.c): `make_status` tracks the last printable left-section character, substitutes space when this setting is on, and fills the unused width before appending any right section.
