# window hold_mode

## Synopsis

```text
/window [<window>] hold_mode [on|off|toggle]
```

## Description

Turn hold mode on or off for the selected window. With no window selector, use the current window. New windows have holding off unless a script enables it. `toggle` reverses the setting; no argument reports it without changing it.

With holding off, new output normally scrolls the window upward to make room. With holding on, output can fill a page and then be held rather than displayed until you acknowledge it. EPIC continues receiving and storing that output; holding does not pause the server or discard messages. A separate scrollback view can also prevent new output from appearing.

Enabling holding starts from the normal scrolling view and advances the held position by the percentage of a page configured by [window hold_slider](hold_slider.md), stopping at the end of available output. Repeating `hold_mode on` while already enabled does not advance it. Turning holding off removes the held view and resumes the normal scrolling view, without deleting queued output. It does not exit a separate scrollback view, so that view can still show older output.

## Reading held output

The [send_line](../bind/send_line.md) binding, normally Enter, advances holding in the input window by the configured hold-slider amount when you submit a line, including an empty line. The timing depends on whether output was already held before the command ran. A zero slider, or a percentage that rounds down to zero rows in a small window, does not advance the held position.

The [scroll_forward](../bind/scroll_forward.md) binding can also advance a held view when it is behind the normal scrolling view. If you are browsing older scrollback, it advances that scrollback view first. See [window hold_slider](hold_slider.md) for Enter's page advancement and [window hold_interval](hold_interval.md) for the displayed queued-line count.

[window flush](flush.md) discards output below the held page without turning holding off. [window flush_scrollback](flush_scrollback.md) discards all stored display output and turns holding off.

## Output and arguments

Queries and valid changes report `Window HOLD_MODE is ON` or `Window HOLD_MODE is OFF`. Invalid values report `Value for HOLD_MODE must be ON, OFF, or TOGGLE` and leave holding unchanged. A query or invalid value stops subsequent operations in the same `/window` command. Additional words after a valid value are processed as subsequent window operations. `/window -hold_mode` is a no-op.

This operation works on invisible windows without making them visible. Changes request output-area and status-bar redraws and send no IRC request.

## Script-provided default

[set hold_mode](../set/hold_mode.md) is provided by the bundled [script/hold_mode](../../script/hold_mode) script, rather than a built-in `/set` variable. When loaded, the script initializes holding to off, applies changes to all windows, and applies its setting to newly created windows. A direct `window hold_mode` changes only the selected window.

## Examples

```text
/window hold_mode on
/window 3 hold_mode toggle
/window 3 hold_mode
/window hold_mode off
```

## History

This operation already existed in ircII2.1.5g.

## Source references

- [source/window.c](../../source/window.c): `new_window`, `windowcmd_hold_mode`, `get_boolean`, `unhold_a_window`, `window_scrollback_forwards`, and `trim_scrollback` implement defaults, state changes, advancement, and retention while holding.
- [source/input.c](../../source/input.c): `send_line` handles Enter's hold advancement and scrollback interaction.
- [source/screen.c](../../source/screen.c): `ok_to_output` and the window redraw logic choose between normal, held, and scrollback views.
- [script/hold_mode](../../script/hold_mode): supplies the optional `/set hold_mode` setting.
