# mousemachine

## Synopsis

```text
/bind <sequence> mousemachine
```

## Description

Consume a character of a terminal mouse report. This action is intended for EPIC's internal `MOUSE` keymap, where printable seven-bit characters are bound to it automatically. It uses the final character of the triggering sequence, ignores stored binding arguments, and does not insert text into the input line.

Enabling [mouse](../set/mouse.md) requests terminal mouse reporting and binds the report prefix `^[[<` to [keymap](keymap.md) `MOUSE`. The remaining report characters accumulate until `M` or `m` is received. EPIC then processes the report, clears the accumulated text, and returns to the normal `HEAD` keymap.

Reports contain a numeric button code, column and row separated by semicolons or colons, followed by `M` or `m`. Button code `64` scrolls backward and `65` scrolls forward in the current window. Code `0` with uppercase `M` selects the visible window at the reported screen row. The column is parsed but does not affect these actions. Other button events have no action. A report ending in `M` or `m` returns to `HEAD` even when its contents are not recognized. Accumulating 63 characters also causes a reset on the next invocation.

## Example

```text
/set mouse on
```

Use this setting to install the mouse bindings and enable reporting on a supporting terminal; manual `mousemachine` bindings are normally unnecessary.

See [bind](../commands/bind.md) for binding syntax and [scroll_backward](scroll_backward.md) and [scroll_forward](scroll_forward.md) for scrolling behavior.

## Source references

- [source/keys.c](../../source/keys.c): `mousemachine`, `handle_mouse_sequence`, `init_mousemachine`, and `set_mouse_support`.
