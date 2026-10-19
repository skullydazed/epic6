# set xterm

## Synopsis

```text
/set xterm [<program>]
/set -xterm
```

Default: `xterm`. With no program, [set](../commands/set.md) displays the current value. Unsetting falls back to the literal program name `xterm` when no environment override exists; it does not disable terminal creation.

## Description

Selects the X terminal program used by [window create](../window/create.md) when it chooses the Xterm backend. EPIC first checks the `XTERM` environment variable, then this setting, then the literal `xterm` fallback. The environment variable takes precedence even if its value is empty.

The selected program must support the launcher argument structure:

```text
<program> -geometry <columns>x<rows> <xterm_options> -e <wserv_path> localhost <port>
```

EPIC derives geometry from the existing screen, adding one to its stored column count. Additional arguments come from [xterm_options](xterm_options.md); [wserv_path](wserv_path.md) selects the compatible helper. Choosing another terminal program works only if it accepts this invocation style.

## Path handling and effects

The program value is a single executable name or pathname, not a shell command. It is passed to `execvp`, which can search `PATH` for a name without a slash. Do not put switches in this value: they are not split from the program name. The launch step does not expand a tilde or interpret shell operators in it.

Changing the setting does not launch a terminal immediately or affect existing screens. It is ignored when creation selects Screen or tmux. Backend selection depends on the environment and [wserv_type](wserv_type.md); setting `xterm` alone does not force an Xterm launch.

EPIC does not validate the executable on assignment. Creation requires foreground operation, the selected program, an available display, and a compatible helper. Failure to launch or connect the helper is handled during creation and can lead to a connection timeout. This setting sends no IRC request.

## Example

```text
/set xterm xterm
/window create
```

This uses the configured name only if no `XTERM` environment override is present and the Xterm backend is selected. For a terminal installed outside the executable search path, supply its actual absolute pathname instead.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration and `xterm` default.
- [source/screen.c](../../source/screen.c): `create_additional_screen` selects the backend and program, constructs geometry and helper arguments, executes with `execvp`, and waits for helper connections.
