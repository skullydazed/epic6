# window hold_slider

## Synopsis

```text
/window [<window>] hold_slider [<number>]
```

## Description

Set how much of a window's output you acknowledge each time you press Enter while [window hold_mode](hold_mode.md) is on. With no window selector, use the current window. `<number>` is a percentage from 0 through 100 of the window's scrolling output height, excluding status bars and toplines.

Traditionally, ircII let a full page scroll between acknowledgments with Enter, while EPIC could freeze output until you used scrollback bindings. This setting supports both styles:

- `100` allows a full page of advancement per acknowledgment, the traditional ircII behavior.
- `0` prevents Enter from advancing the held view, the traditional EPIC behavior. You can still use [scroll_forward](../bind/scroll_forward.md) to advance held output, or turn hold mode off.
- Intermediate values advance part of a page, retaining some overlap for reading.

The actual advancement is `number × output_rows / 100`, rounded down to whole rows and stopped at the end of available output. For a 21-row output area, 50 advances 10 rows. Small percentages can round down to zero, as does any percentage in a zero-height output area.

The [send_line](../bind/send_line.md) binding, normally Enter, performs this advancement in the input window when you submit a line, including an empty line. It does not automatically advance another window merely because your command selects that window. The same slider calculation is used when initially enabling hold mode. Changing the slider while already holding does not itself advance or release output.

## Defaults and arguments

New windows copy [set hold_slider](../set/hold_slider.md), whose compiled default is 100. Changing that `/set` value affects subsequently created windows; this operation overrides the value for one existing window.

Without an argument, report the current value. A query or successful change reports `Window hold slider is <number>`. Values converting to less than 0 or greater than 100 report `Hold slider must be 0 to 100!` and leave the setting unchanged. Use ordinary decimal integers; the current numeric parser also accepts base notation and numeric prefixes, and nonnumeric text can convert to zero.

There are no other options. Additional words are processed as subsequent window operations. `/window -hold_slider` is a no-op. The operation works on invisible windows, does not enable hold mode, and sends no IRC request. It does not control the independent amount used by scrollback bindings.

## Examples

```text
/window hold_slider 100
/window 3 hold_slider 50
/window 3 hold_slider
/window 3 hold_slider 0
```

Use full-page advancement in the current window, half-page advancement in window 3, report window 3's setting, or disable its Enter-based advancement.

## History

This operation first appeared in EPIC4-1.1.13.

## Source references

- [source/window.c](../../source/window.c): `new_window`, `windowcmd_hold_slider`, `windowcmd_hold_mode`, and `unhold_a_window` implement the default, range, and percentage calculations; `window_scrollback_forwards` handles independent forward scrolling.
- [source/input.c](../../source/input.c): `send_line` performs Enter's hold advancement.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): register `hold_slider` without a change callback and define its compiled default.
- [source/ircaux.c](../../source/ircaux.c): `my_atol` implements numeric conversion.
