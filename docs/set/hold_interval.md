# set hold_interval

## Synopsis

```text
/load hold_mode
/set hold_interval [<lines>]
```

This `/set` variable is supplied by [script/hold_mode](../../script/hold_mode), not registered as a C built-in. Loading that script defines the setting and initializes it to `10`; without a script providing it, the built-in `set` command treats the name as unknown. With the supplied setting loaded, omitting the value queries it.

## Description

Controls the increments used for the held-line count in window status bars. The traditional value is `10`; use `1` to show the exact count. The supplied script applies a change to all existing windows through [window hold_interval](../window/hold_interval.md), and installs an [on window_create](../on/window_create.md) handler to apply it to new windows.

The underlying `%B` status field, formatted by [status_hold_lines](status_hold_lines.md), rounds the queued line count down to a multiple of the window's interval. With interval 10, counts below 10 produce no held-lines text, counts 10 through 19 display 10, and counts 20 through 29 display 20. It considers output below the visible page in hold or scrollback mode, using whichever view is farther from the end.

This reduces changes to the displayed count. It is not a refresh timer and does not prevent status updates caused by other events. It does not enable hold mode, limit the buffer, or select how many lines are released by Enter; those are separate window controls.

## Values and script scope

Use ordinary positive integers from `1` through `32767`. The underlying window command rejects zero and negative values with `Hold interval must be a positive value!`; its storage is a short integer without an upper-bound check, so larger values are not reliable. A script setting assignment is not a guarantee that every underlying window accepted an unsuitable value.

A direct `/window 3 hold_interval 1` changes only window 3. A later script-provided `/set hold_interval` assignment applies its value to all windows again. Loading `hold_mode` also initializes its separate `hold_mode` setting to off and applies that to existing windows, so loading the compatibility script is not a passive query of current window settings.

These are local display controls and send no IRC request.

## Example

```text
/load hold_mode
/set hold_interval 1
```

Show exact held-line counts through the supplied global compatibility setting. For one window without loading the script, use `/window hold_interval 1` instead.

## Source references

- [script/hold_mode](../../script/hold_mode): defines the integer setting, initializes it, applies it to existing windows, and installs the new-window handler.
- [script/addset](../../script/addset): supplies script-defined settings.
- [source/window.c](../../source/window.c): new windows start with interval 10; `windowcmd_hold_interval` validates and stores per-window values.
- [source/status.c](../../source/status.c): `status_hold_lines` rounds the displayed count.
- [source/vars.c](../../source/vars.c): current built-in registration omits `hold_interval`.
