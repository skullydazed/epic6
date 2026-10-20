# hold_mode

## Synopsis

```text
/load hold_mode
/set hold_mode [on|off|toggle]
/set hold_interval [<lines>]
```

## Description

This script restores the global `set hold_mode` and `set hold_interval` interface familiar to ircII users. EPIC6 implements holding separately for each window; these two settings are script-defined wrappers that apply the same value to all windows and supply values to newly created windows.

The script loads [addset](addset.md) and uses `winrefs` from [builtins](builtins.md), normally loaded first by the default [global](global.md) configuration. If loading it independently, ensure `builtins` or an equivalent `winrefs` definition is available.

## Settings

| Setting | Initial value | Effect |
| --- | --- | --- |
| `hold_mode` | `off` | Apply [window hold_mode](../window/hold_mode.md) on or off to every existing window returned by `winrefs()`. |
| `hold_interval` | `10` | Apply [window hold_interval](../window/hold_interval.md) to every existing window. This controls the held-line count's displayed increments, not a time interval. |

Loading or reloading initializes both settings and applies those initial values immediately. In particular, loading turns off holding in existing windows. A later assignment applies to visible and hidden windows across servers, replacing any individual overrides. Querying a setting without a value reports its stored global value without reapplying it.

A numbered `window_create` hook applies the stored values to every new window. Direct per-window changes remain possible, but they do not update the global settings. For example, `/window 3 hold_mode on` affects only window 3; a subsequent `/set hold_mode off` overrides it along with the other windows. Other creation hooks or later commands can likewise replace this script's choices.

Use positive integers from `1` through `32767` for `hold_interval`. Its script setting has generic nonnegative-integer validation, but the per-window operation rejects zero and stores its value in a short integer without checking the upper limit. Consequently, assigning zero can leave the global setting at zero while individual windows retain their previous interval, and oversized values can produce invalid per-window results. The wrapper does not roll back or verify every window update.

## Reading held output

Holding lets a window fill a page before displaying further output, while EPIC continues receiving and storing new output. It does not pause the server. Turning holding off resumes the normal scrolling view without discarding queued output; a separate scrollback view can still show older text.

The amount acknowledged by Enter is controlled independently by [hold_slider](../set/hold_slider.md), not by `hold_interval`. A slider of `100` gives traditional full-page advancement and is the current compiled default. The global `set hold_slider` affects future windows only; use [window hold_slider](../window/hold_slider.md) to change an existing window. The `hold_mode` script does not change slider values itself.

These wrappers send no IRC requests and have no function return value. Their effects and diagnostics come from the setting parser and the underlying window operations; errors can leave windows with different values.

## Examples

```text
/load hold_mode
/set hold_mode on
/set hold_interval 10
/window hold_slider 100
```

Enable holding globally, show held counts in increments of ten lines, and request full-page acknowledgment in the current window. To stop holding globally, use `/set hold_mode off`.

## History

This script first appeared in EPIC4-1.1.13.

## Source references

- [script/hold_mode](../../script/hold_mode): global callbacks, initialization, and new-window hook.
- [script/builtins](../../script/builtins) and [script/addset](../../script/addset): `winrefs` and script-defined setting support.
- [source/window.c](../../source/window.c): hold mode, interval and slider operations, and `window_create` dispatch.
- [source/vars.c](../../source/vars.c): generic setting validation and global slider default.
- [source/input.c](../../source/input.c): Enter's hold advancement behavior.
