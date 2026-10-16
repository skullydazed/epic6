# window notify_name

## Synopsis

```text
/window [<window>] notify_name [<text>]
/window [<window>] notify_name -
/window [<window>] -notify_name
```

## Description

Set the text used to identify the selected window in the `%{1}F` status expando. With no window selector, use the current window. This is useful for scripts that display customized or colored notification labels.

When a window is [notified](notified.md), `%F` lists its reference number. `%{1}F` instead uses the first available value:

1. The window's notify name.
2. Its [window name](name.md).
3. Its reference number.

Both lists are formatted through [set status_notify](../set/status_notify.md). The notify name only changes the label: it does not mark the window notified, enable [window notify](notify.md), change notification levels, or alter the separate activity indicator. New windows have no notify name.

The value can be a double-quoted string containing spaces. It need not be unique and may be numeric. It is not a window lookup name: commands still refer to the window by its ordinary name or refnum. An explicitly empty string is distinct from an unset value and can produce an empty label rather than falling back to the window name; use the hyphen forms to unset it.

## Queries and output

Without an argument, report `Window NOTIFY NAME is <text>` or `Window NOTIFY NAME is unset`. Assigning a different label reports `Window NOTIFY NAME changed to <text>`. Reassigning the same label, ignoring case, reports `Window NOTIFY NAME is <text>` and stores the supplied capitalization.

`notify_name -` and `-notify_name` both clear the value and report `Window NOTIFY NAME unset`. Assignments and clearing request a status update for the selected window; this handler does not explicitly request updates for every other window displaying the global notification list.

Additional words after the argument are processed as subsequent window operations. The operation works on visible and invisible windows without changing visibility or input selection, and sends no IRC request.

## Examples

```text
/window 3 notify_name chat
/window 3 notify_name "private messages"
/window 3 notify_name
/window 3 notify_name -
```

Assign a short label, replace it with a label containing spaces, report it, or clear it. The label is used when window 3 is notified and the status format includes `%{1}F`.

## History

This operation first appeared in EPIC5-0.0.3.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_notify_name`, `new_window`, and `get_window_notify_name` implement assignment, clearing, reports, and the unset default.
- [source/status.c](../../source/status.c): `status_notify_windows` and the two `F` mappings implement refnum versus notify-name fallback behavior.
- [include/words.h](../../include/words.h) and [source/ircaux.c](../../source/ircaux.c): `new_next_arg` and `universal_next_arg_count` parse quoted labels.
