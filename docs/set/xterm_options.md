# set xterm_options

## Synopsis

```text
/set xterm_options [<options>]
/set -xterm_options
```

Default: unset, adding no options. With no options, [set](../commands/set.md) displays the current value. Unsetting removes the extra arguments.

## Description

Supplies extra command-line arguments to the X terminal program when [window create](../window/create.md) selects the Xterm backend. The program is selected through the `XTERM` environment variable or [xterm](xterm.md) setting. This does not apply to ordinary [window](../commands/window.md) operations within an existing screen.

The invocation is constructed in this order:

```text
<program> -geometry <columns>x<rows> <xterm_options arguments> -e <wserv_path> localhost <port>
```

EPIC splits the stored options with its argument parser, which honors double-quoted words, and passes the arguments directly to `execvp`. Shell pipelines, redirections, and command substitutions are not interpreted by this launch step. Ordinary EPIC command parsing can still expand expressions while assigning the value.

The external program determines which options are valid and how it handles them. EPIC does not validate flags, resolve conflicting options, or guarantee that an alternate terminal accepts Xterm's argument syntax. Keep extra options compatible with the appended helper command.

## Effects and requirements

Changes apply to future Xterm-backend launches, not existing terminal windows. They do not launch a window immediately or force this backend; selection depends on the environment and [wserv_type](wserv_type.md). Screen and tmux launches read their separate option settings.

Creation requires foreground operation, an available X display, an executable terminal program, and a compatible [wserv_path](wserv_path.md). Launch and helper-connection failures are reported during creation and can result in a timeout. The launcher uses a fixed 64-entry argument array without a per-option bounds check, so avoid long argument lists. This setting sends no IRC request.

## Example

Remove extra options and use the selected terminal's defaults, in addition to EPIC's own geometry and helper arguments:

```text
/set -xterm_options
/window create
```

The second command uses these arguments only if the Xterm backend is selected.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration and unset default.
- [source/screen.c](../../source/screen.c): `create_additional_screen` selects the backend and executable, splits options, appends helper arguments, launches directly, and waits for connections.
