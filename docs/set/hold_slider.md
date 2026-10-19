# set hold_slider

## Synopsis

```text
/set hold_slider [<number>]
```

Default: `100`. With no number, [set](../commands/set.md) displays the current value. This global integer supplies the initial hold-slider value for newly created windows. It does **not** change the current window's slider or any other existing window; use [window hold_slider](../window/hold_slider.md) for those.

## Description

A window's hold slider is the percentage of its scrolling output height acknowledged when you submit a line through [send_line](../bind/send_line.md), normally Enter, while holding output. The same percentage is used when initially enabling [window hold_mode](../window/hold_mode.md).

- `100` advances up to a full page, the traditional ircII behavior.
- `0` prevents Enter from advancing the held view. Forward-scrolling bindings or turning hold mode off can still reveal output.
- Intermediate values advance part of a page, retaining overlap for reading.

Advancement is `slider × output_rows / 100`, rounded down to whole rows and stopped at the end of available output. Small percentages can round down to zero. Status bars and toplines are excluded from the scrolling output height.

This setting does not enable holding, determine held-line count display increments, or control the independent amount used by scrollback bindings. Changing the global default neither advances an existing held view nor rearranges its output.

## Values and limits

Use a decimal percentage from `0` through `100`. The generic `/set` integer handler rejects negative and nonnumeric input but does not enforce a 100-percent upper bound for this setting. New windows copy the value into short-integer storage; larger values are outside the intended percentage range and sufficiently large values can overflow that storage.

The per-window command does enforce `0` through `100`. Setting the global default to an out-of-range value is not a substitute for that command's validation. There is no immediate callback when this setting changes, and it sends no IRC request.

## Example

Give future windows half-page hold advancement, and separately update the current window:

```text
/set hold_slider 50
/window hold_slider 50
```

Restore the default for future windows with `/set hold_slider 100`. Existing windows retain their own values until changed explicitly.

## History

This setting first appeared in EPIC4-1.1.13.

## Source references

- [source/window.c](../../source/window.c): `new_window` copies the global value; `unhold_a_window` and `windowcmd_hold_mode` calculate advancement; `windowcmd_hold_slider` validates per-window percentages.
- [source/input.c](../../source/input.c): `send_line` advances holding in the input window.
- [source/vars.c](../../source/vars.c): global integer registration has no callback and uses generic non-negative integer validation.
- [include/config.h](../../include/config.h): default 100.
