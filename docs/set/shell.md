# set shell

## Synopsis

```text
/set shell [<program>]
/set -shell
```

Default: `/bin/sh`. With no program, [set](../commands/set.md) displays the current value. Unsetting selects direct execution for subsequent processes rather than restoring the default shell.

## Description

Names the program normally spawned to execute commands launched by [exec](../commands/exec.md). This is generally a command shell, although it can be another program compatible with the arguments EPIC supplies. Required command-line switches belong in [shell_flags](shell_flags.md).

The shell invocation consists of the configured program, the entire `shell_flags` string as one argument, and the command text as one argument. With the defaults, this corresponds to:

```text
/bin/sh -c <command text>
```

The program value is used directly as an executable pathname with `execl`; EPIC does not search `PATH` for it or split it into a program and options. Set a usable pathname, and keep switches out of this value. The default is not selected from the `SHELL` environment variable.

## Direct execution and effects

`exec -direct` bypasses this setting for that process. Unsetting `shell` also selects the direct path: EPIC splits the command into arguments, honors double-quoted words, and uses `execvp`, which can search `PATH`. Shell-specific pipelines, redirections, wildcard expansion, and variable expansion are not provided by that direct launch step.

Changing this setting does not launch a program or alter processes already running. EPIC does not validate the executable when assigning it. A launch failure is reported by the child as `Error running program ...`, followed by the operating system's error text.

Exec processes receive pipes for their standard streams and `TERM=tty`, not an interactive terminal. Choosing a shell does not make `exec` an interactive terminal session. Available shells and their command syntax depend on the local system; this setting sends no IRC request.

## Example

Restore the standard shell and flags explicitly, then execute a command:

```text
/set shell /bin/sh
/set shell_flags -c
/exec echo hello
```

To bypass the shell for one invocation, use `/exec -direct echo hello` instead.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration and `/bin/sh` default.
- [source/exec.c](../../source/exec.c): `start_process` selects shell or direct execution, sets up pipes and `TERM`, and reports launch failures; exec option parsing handles `-direct`.
