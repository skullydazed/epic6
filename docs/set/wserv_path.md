# set wserv_path

## Synopsis

```text
/set wserv_path [<program>]
/set -wserv_path
```

Default: the helper installation pathname compiled into EPIC's build. It depends on the installation configuration. With no program, [set](../commands/set.md) displays the value. Unsetting removes the helper path and prevents normal [window create](../window/create.md) from launching a new screen.

## Description

Selects the `wserv` helper used by `window create`. The helper runs in a new terminal or multiplexer window and connects back to EPIC over local loopback sockets, carrying terminal input, output, and control information. This is for creating an additional screen, not an ordinary split window in the existing screen.

The launcher appends `localhost` and a dynamically chosen port to the helper invocation. It uses this value with the selected Screen, Xterm, or tmux backend; related options come from [screen_options](screen_options.md), [xterm_options](xterm_options.md), or [tmux_options](tmux_options.md).

Use an executable helper compatible with the EPIC binary. The current control protocol expects version `4`; a reported version mismatch produces a diagnostic and removes that screen. The build's installed helper is produced as `wserv4`.

## Path handling and errors

EPIC stores the string without checking the executable when you assign it. Prefer an absolute pathname. This is not a list of search directories, and EPIC does not perform a tilde or shell expansion of it in the launcher. For Screen and Xterm it is a single argument; the tmux branch constructs a command string containing it without quoting, so paths containing spaces or shell metacharacters are not handled uniformly. Keep the value a straightforward executable pathname without embedded options.

Creation also requires foreground operation and an available backend selected from the terminal environment. If this setting is unset, creation reports that `wserv_path` must be set. Failures to start or connect the helper are reported during creation, which can time out waiting for its connections.

Changing this setting affects future helper launches. It does not replace helpers already running, create a screen immediately, or send an IRC request.

## Example

If your compatible helper is installed at this pathname:

```text
/set wserv_path /usr/local/bin/wserv4
/window create
```

Use the actual path from your installation. The second command launches only when the remaining terminal and backend requirements are met.

## History

`set wserv_path` first appeared in EPIC4-1.1.3.

## Source references

- [source/vars.c](../../source/vars.c): string registration and build-supplied `WSERV_PATH` default.
- [Makefile.in](../../Makefile.in) and [source/Makefile.in](../../source/Makefile.in): helper installation and compiled pathname.
- [source/screen.c](../../source/screen.c): `create_additional_screen` launches backend-specific helper commands; control handling enforces the expected version.
- [source/wserv.c](../../source/wserv.c): helper host/port arguments and protocol handshake.
