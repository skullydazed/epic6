# set wserv_type

## Synopsis

```text
/set wserv_type [screen|xterm|tmux]
/set -wserv_type
```

Default: `screen`. With no value, [set](../commands/set.md) displays the current value. Names are case-insensitive. Unsetting leaves backend selection to its environment-based fallback.

## Description

Selects a preference for [window create](../window/create.md) when a terminal multiplexer and an X display are both available. In particular, running under GNU Screen with an X display leaves a choice between another Screen window and a separate Xterm window. EPIC also supports the corresponding tmux case.

This is not an unconditional backend selector. The launcher checks environment variables in the following order; the first matching row applies:

| Environment present | Selection |
| --- | --- |
| `STY` and `DISPLAY` | `xterm` selects Xterm; `screen`, `tmux`, or unset selects Screen. |
| `TMUX` and `DISPLAY` | `xterm` selects Xterm; `screen`, `tmux`, or unset selects tmux. |
| `TMUX` | tmux, regardless of this setting. |
| `STY` | Screen, regardless of this setting. |
| `DISPLAY` and `TERM` | Xterm, regardless of this setting. |
| None of those combinations | Creation fails because EPIC cannot choose a backend. |

The checks test whether variables exist, not whether their contents are nonempty. If both `STY` and `TMUX` are present with `DISPLAY`, the first row wins. Without `DISPLAY`, tmux takes precedence over Screen. Selecting `tmux` while in the first row does not launch tmux; it falls back to that row's Screen backend.

## Effects and errors

An unsupported value produces `SET WSERV_TYPE must be either SCREEN, XTERM or TMUX` and clears the setting; it does not retain the previous value. Later creation then uses the fallback shown above.

Changing the setting does not immediately create a screen or reconfigure an existing helper. A later creation still requires foreground operation, a compatible [wserv_path](wserv_path.md), and the selected external program. Backend options come from [screen_options](screen_options.md), [tmux_options](tmux_options.md), or [xterm_options](xterm_options.md). This setting sends no IRC request.

## Example

Prefer Xterm when already running in Screen or tmux with an X display available:

```text
/set wserv_type xterm
/window create
```

Without the relevant display environment, the preference cannot force Xterm. Use `/set -wserv_type` to restore environment-based fallback selection.

## History

The backend-selection setting was introduced in EPIC4-1.1.14.

## Source references

- [source/vars.c](../../source/vars.c): registration and `set_wserv_type` validation, including clearing unsupported values.
- [include/config.h](../../include/config.h): `screen` default.
- [source/screen.c](../../source/screen.c): `create_additional_screen` implements ordered environment tests and backend-specific launches.
