# set screen_options

## Synopsis

```text
/set screen_options [<options>]
/set -screen_options
```

Default: unset, adding no options. With no options, [set](../commands/set.md) displays the current value. Unsetting removes the extra arguments.

## Description

Supplies extra command-line arguments to the `screen` program when [window create](../window/create.md) chooses the GNU Screen backend to create a new EPIC screen. It does not apply to ordinary [window](../commands/window.md) creation within an existing terminal screen.

EPIC constructs the invocation in this order:

```text
screen <screen_options arguments> <wserv_path> localhost <port>
```

The options are split into words with EPIC's argument parser, which honors double-quoted arguments. They are passed directly to `execvp`, not through a shell: shell pipelines, redirections, and command substitutions are not evaluated by this launch step. Ordinary EPIC command expansion can still occur when you assign the setting.

EPIC does not validate these options. Accepted flags and their effects depend on the installed Screen program. Keep them compatible with the appended helper command; options that change Screen's action can prevent the new EPIC screen from connecting.

## Backend and requirements

The setting is read only in the Screen branch. Screen selection uses the `STY` environment variable; when an X display is also available, `wserv_type` can select the Xterm branch instead. An Xterm launch reads [xterm_options](xterm_options.md), while a tmux launch reads [tmux_options](tmux_options.md).

Creating the screen requires foreground operation, an executable `screen` available through the process search path, a configured [wserv_path](wserv_path.md), and a compatible helper that connects back over local loopback sockets. Merely assigning these options does not create a screen or check that those requirements are met. Launch or helper-connection failures are reported by the creation operation, which can time out waiting for the helper.

The launcher uses a fixed 64-entry argument array without a per-option bounds check. Avoid long argument lists. Changing the setting affects future Screen launches and does not reconfigure existing Screen windows or sessions.

## Example

Remove previously configured extra options, then use the backend's ordinary defaults for a later creation:

```text
/set -screen_options
/window create
```

The second command uses Screen only when the backend-selection conditions choose it; it does not force that backend.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration and unset default.
- [source/screen.c](../../source/screen.c): `create_additional_screen` selects the backend, parses options, constructs the direct invocation, and waits for helper connections.
