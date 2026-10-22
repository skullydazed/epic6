# window status_prefix_when_current

## Synopsis

```text
/window [<window>] status_prefix_when_current ["<text>"]
```

## Description

Set the selected window's replacement for the `%{1}P` status expando when that window is its screen's input window. With no selector, use the current window. This condition is evaluated per screen, rather than against one global current window.

The text is one argument; quote it if it contains spaces. Setting it requests a status redraw. The status format must contain `%{1}P` for this prefix to appear; see [status_format](status_format.md).

No argument clears the window override, allowing [set status_prefix_when_current](../set/status_prefix_when_current.md) to supply the value. It does not report the current text. `/window -status_prefix_when_current` does nothing. See [status_prefix_when_not_current](status_prefix_when_not_current.md) for the other state.

## Examples

```text
/window 3 status_prefix_when_current "> "
/window 3 status_prefix_when_current
```

## Source references

`windowcmd_status_prefix_when_current` in [source/window.c](../../source/window.c); `status_window_prefix`, its expando registration, and `IS_CURRENT_WINDOW` in [source/status.c](../../source/status.c).
