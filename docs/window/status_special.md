# window status_special

## Synopsis

```text
/window [<window>] status_special ["<text>"]
```

## Description

Set window-specific text returned by the `%.` status expando. With no window selector, use the current window. This provides a place for a script or user to display information belonging to one window without changing the global status format.

The text appears only where the active status format includes `%.`. It can be used in the single-row [window status_format](status_format.md) or either double-status format. New windows have no special text, so `%.` initially produces an empty string. Changing this value does not alter any other window's special text or add `%.` to a format automatically.

The status expando inserts the stored text directly; percent-based status expandos inside it are not recursively compiled. The completed status line can still undergo ordinary alias expansion when [set status_does_expandos](../set/status_does_expandos.md) is enabled. See [set status_format](../set/status_format.md) for status formatting.

## Arguments and effects

The text is one argument; use double quotes for spaces. Without an argument, clear the stored text. This is **not a query**. An empty quoted string stores empty text and also makes `%.` display nothing. `/window -status_special` is a no-op rather than a clearing operation.

Additional words after a supplied text argument are processed as subsequent window operations. To clear the value, put ordinary `status_special` at the end of the command, because a following word would be consumed as text.

The operation requests a status redraw and normally produces no confirmation message. It works on invisible windows without showing them, does not change visibility, status-row count, or input selection, and sends no IRC request.

## Examples

```text
/window 3 status_format "Window %R: %."
/window 3 status_special "monitoring alerts"
/window 3 status_special
```

Include `%.` in window 3's single-row status, set its special text, then clear the text. The format remains in place after clearing; it displays an empty value for `%.`.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_status_special` stores or clears the value and requests redraw; `new_window` initializes it unset.
- [source/status.c](../../source/status.c): the `.` mapping and `status_windowspec` insert the stored text; `make_status` optionally performs alias expansion on the completed status line.
