# set hold_mode

## Synopsis

```text
/load hold_mode
/set hold_mode [on|off|toggle]
```

This setting is supplied by [script/hold_mode](../../script/hold_mode), not registered as a C built-in. Loading that script initializes it to `off`. Without a script providing the name, the built-in `set` command treats it as unknown. With the supplied setting loaded, omitting the value queries it and `toggle` reverses its stored boolean value.

## Description

When enabled through the supplied script, hold mode lets each window fill a page with new output and then hold later output until acknowledged. EPIC continues receiving and storing messages; it does not pause the server. Submitting a line with the [send_line](../bind/send_line.md) binding, normally Enter, advances the input window's held view, including when the submitted line is empty.

The amount advanced is set by [window hold_slider](../window/hold_slider.md), normally a page, rather than by this boolean setting. A zero slider or an amount rounded to zero rows will not advance it. [Hold_interval](hold_interval.md) controls displayed count increments, not the amount released.

## Existing and new windows

The supplied setting applies changes to **all existing windows** using [window hold_mode](../window/hold_mode.md), and an [on window_create](../on/window_create.md) handler applies its stored value to newly created windows. It is not merely a default for future windows.

A direct `/window 3 hold_mode on` affects only window 3 and does not update the script's global setting. A later `/set hold_mode` change applies the global value to all windows again. Toggling the script setting reverses its stored value and distributes that result; it does not independently reverse each window's possibly different state.

Loading the compatibility script itself applies `hold_mode off` and `hold_interval 10` to existing windows. Load it before customizing those global settings rather than treating loading as a passive query.

Turning holding off resumes the normal scrolling view without deleting queued output. A separate scrollback view can still keep older output visible. Repeating `on` for an already-held window does not advance its view. These controls are local and send no IRC request.

## Example

Enable holding globally through the supplied compatibility script:

```text
/load hold_mode
/set hold_mode on
```

Submit empty lines with Enter to read held output in the input window. To enable it only for one window without the global script setting, use `/window hold_mode on` instead.

## Source references

- [script/hold_mode](../../script/hold_mode): boolean setting, initial assignments, all-window callback, and new-window handler.
- [script/addset](../../script/addset): script-defined setting support.
- [source/window.c](../../source/window.c): `windowcmd_hold_mode` changes per-window held views and requests redraws.
- [source/input.c](../../source/input.c): `send_line` acknowledges held output.
- [source/vars.c](../../source/vars.c): current built-in registration omits `hold_mode`.
