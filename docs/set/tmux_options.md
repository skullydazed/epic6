# set tmux_options

## Synopsis

```text
/set tmux_options [<text>]
/set -tmux_options
```

Default: unset. With no value, [set](../commands/set.md) displays the current value. The hyphen form clears the string.

## Description

Supplies extra arguments to `tmux` when [window create](../window/create.md) selects the tmux backend. EPIC constructs this invocation:

```text
tmux <tmux_options arguments> new-window <helper command>
```

The helper command contains [wserv_path](wserv_path.md), `localhost`, and a local listening port. Extra options go before `new-window`; they are tmux global options, not options appended to that subcommand. EPIC splits them with its argument parser, honoring double quotes, and launches tmux directly with `execvp`. It does not interpret these options through a shell.

Backend selection depends on `TMUX`, `STY`, `DISPLAY`, and [wserv_type](wserv_type.md). This setting neither forces tmux selection nor creates a screen by itself. Creation requires foreground operation, tmux on the executable search path, and a working helper. Changing the setting affects future launches only. Unsetting adds no extra arguments. Keep the argument list short: the launcher uses a fixed 64-entry array.

## Example

```text
/set -tmux_options
/window create
```

## Source references

- [source/screen.c](../../source/screen.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
