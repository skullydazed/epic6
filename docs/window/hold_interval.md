# window hold_interval

## Synopsis

```text
/window [<window>] hold_interval [<lines>]
```

## Description

Control the increments used to display the number of held lines in the selected window's status bar. With no window selector, use the current window. The built-in default for each new window is 10, the traditional value. Use 1 to display the exact count.

The `%B` status expando, formatted by [set status_hold_lines](../set/status_hold_lines.md), rounds the queued line count down to a multiple of this interval. With interval 10, counts 1 through 9 produce no held-lines text, 10 through 19 display 10, and 20 through 29 display 20. The count includes output below the visible page while holding or scrolling backward; it uses whichever view is farther from the end of the buffer.

This reduces changes to the displayed count rather than imposing a timer or preventing other status-bar updates. It does not enable [window hold_mode](hold_mode.md), set a buffer limit, or control how many lines are released when you press Enter; use [window hold_slider](hold_slider.md) for hold advancement.

## Arguments and output

`<lines>` must yield a positive number. Zero, negative values, and text that converts to zero report `Hold interval must be a positive value!` and leave the setting unchanged. Without an argument, report the current value. A successful change also reports `Window hold interval notification is <number>`.

Use ordinary positive integers in the range 1 through 32767. The parser accepts numeric prefixes and base notation, but the value is stored in a short integer without checking its upper limit, so larger values can produce an invalid interval.

Additional words are processed as subsequent window operations. `/window -hold_interval` is a no-op. This operation works for invisible windows and sends no IRC request.

## Script-provided default

[set hold_interval](../set/hold_interval.md) is supplied by the bundled [script/hold_mode](../../script/hold_mode) script, rather than by a built-in `/set` variable. When loaded, that script applies its setting to existing windows and uses it for newly created windows. A direct `window hold_interval` changes only the selected window; a later script-provided `/set hold_interval` applies to all windows again.

## Examples

```text
/window hold_interval 1
/window 3 hold_interval 10
/window 3 hold_interval
```

Show exact held-line counts in the current window, use increments of ten in window 3, and report window 3's interval.

## Source references

- [source/window.c](../../source/window.c): `new_window`, `windowcmd_hold_interval`, and `get_window_hold_interval` define the default, parsing, storage, and reporting; `add_to_scrollback` requests status updates as queued output grows.
- [source/status.c](../../source/status.c): the `%B` mapping and `status_hold_lines` implement rounded held-line counts.
- [source/ircaux.c](../../source/ircaux.c): `my_atol` performs numeric conversion.
- [script/hold_mode](../../script/hold_mode): defines and applies the optional `/set hold_interval` setting.
