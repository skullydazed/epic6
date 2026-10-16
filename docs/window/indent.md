# window indent

## Synopsis

```text
/window [<window>] indent [on|off|toggle]
```

## Description

Enable or disable continuation-line indentation for an individual window. With no window selector, use the current window. `on` enables indentation, `off` disables it, and `toggle` reverses the setting. Without an argument, report the current value.

When output wraps onto multiple display rows, indentation normally aligns the continuation text with the start of the second word on the first row. The continuation prefix comes from [set continued_line](../set/continued_line.md): with indentation enabled, EPIC pads or truncates that prefix to the indentation width. If the detected indentation is nonpositive or exceeds one third of the output width, EPIC uses one third of the width instead. Word boundaries are determined by [set word_break](../set/word_break.md).

With indentation off, wrapped rows still use the `continued_line` prefix at its own width. Turning indentation off therefore does not necessarily remove the continuation marker.

## Defaults and effects

New windows copy [set indent](../set/indent.md), whose compiled default is off. Changing `/set indent` updates **all existing windows**, including invisible windows, overwriting their individual `window indent` settings. A later `window indent` can override one window again.

Changing this flag affects output as it is wrapped. It does not itself rebuild existing scrollback or request a redraw. Use [window rebuild_scrollback](rebuild_scrollback.md) if you want retained output reformatted with the current setting. This operation works for invisible windows without making them visible and sends no IRC request.

## Output and arguments

Queries and valid changes report `Window INDENT is ON` or `Window INDENT is OFF`. Invalid values report `Value for INDENT must be ON, OFF, or TOGGLE` and leave the setting unchanged. A query or invalid value stops subsequent operations in the same `/window` command. Additional words after a valid value are processed as subsequent window operations. `/window -indent` is a no-op.

## Examples

```text
/window indent on
/window 3 indent off
/window 3 indent
/window 3 indent toggle rebuild_scrollback
```

Enable indentation in the current window, disable it in window 3, report window 3's setting, or toggle it and rebuild that window's scrollback.

## Source references

- [source/window.c](../../source/window.c): `new_window`, `windowcmd_indent`, `get_boolean`, `set_window_indent`, and `windowcmd_rebuild_scrollback` implement defaults, changes, reporting, and explicit rebuilding.
- [source/screen.c](../../source/screen.c): `prepare_display` implements word wrapping, indentation width, and continuation-prefix formatting.
- [source/vars.c](../../source/vars.c): `set_indent` propagates `/set indent` to every window.
- [include/config.h](../../include/config.h): defines the compiled indentation default.
