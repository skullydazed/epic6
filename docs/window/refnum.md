# window refnum

## Synopsis

```text
/window [<window>] refnum <destination>
```

## Description

Make the specified destination window EPIC's global current window. The destination can be a reference number, a [window name](name.md), or a window UUID. Names and UUIDs are compared case-insensitively across all windows, without restricting lookup to the selected window's screen or server.

If the destination is visible, it also becomes its screen's input window, updating that screen's previous-window history. If it is hidden, it remains hidden and no screen's input selection is changed. Use [window refnum_or_swap](refnum_or_swap.md) to swap a hidden destination into view instead.

Selecting a hidden window as globally current is useful for commands that operate on the current window, but it does not create a visible input area for it. Do not confuse global current selection with a screen's input window. The selection can also affect the server context used by subsequent commands.

This operation does not rename or renumber windows, change their geometry, or move their contents. It accepts skipped or unswappable destinations because it does not perform a swap. A change of global current window invokes the `switch_windows` hook. No IRC request is sent, and successful selection normally produces no confirmation message.

## Arguments and errors

`<destination>` is required and consumed as one whitespace-delimited word. Use a refnum or UUID to refer reliably to a window whose name contains spaces. This explicit operation can also select a name that would otherwise be interpreted as a `/window` operation.

A missing destination reports `REFNUM: Please specify a window refnum or name`. An unknown destination reports `REFNUM: No such window: <destination>`. Both stop subsequent operations in the same `/window` command without changing selection.

Following words are processed as subsequent window operations on the destination. `/window -refnum` is a no-op. An optional selector before `refnum` does not limit the destination search.

## Examples

```text
/window refnum 3
/window refnum chat
/window refnum 3 name chat
```

Select window 3, select the window named `chat`, or select window 3 and then name it `chat`. If the destination is hidden, all three forms leave it hidden.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_refnum`, `get_window_from_args`, and `get_window_by_desc` implement destination lookup, errors, and selection; `make_window_current_by_refnum` invokes the switch hook; `set_screens_current_window` updates input selection and history only for visible destinations.
