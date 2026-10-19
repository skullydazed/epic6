# set shell_flags

## Synopsis

```text
/set shell_flags [<flags>]
/set -shell_flags
```

Default: `-c`. With no flags, [set](../commands/set.md) displays the current value. Unsetting removes the stored string, but the launcher still supplies an empty argument in its place.

## Description

Contains the flags passed to the command [shell](shell.md) used by [exec](../commands/exec.md). The default `-c` asks `/bin/sh` to interpret the following argument as command text. Other programs can require different options; their accepted flags and interpretation depend on the installed shell.

The entire value is passed as one argument, followed by the entire exec command as one more argument. EPIC does not split this setting at spaces or remove embedded quoting during launch. For example, storing `-c -x` does not supply separate `-c` and `-x` arguments. Use an option form accepted as a single argument by the chosen program.

With the defaults, the argument structure is:

```text
argv[0] = /bin/sh
argv[1] = -c
argv[2] = <exec command text>
```

Unsetting this setting produces `argv[1]` as an empty string rather than omitting it or selecting direct execution. Whether that invocation is useful depends on the program; unsetting does not reliably disable shell interpretation. Use `exec -direct` to bypass the shell for one process, or unset `shell` to select direct execution generally. Those paths do not read `shell_flags`.

## Effects and errors

Changing the setting affects subsequent shell launches and does not alter running processes or launch anything itself. EPIC does not validate the flags when assigning them. Invalid options are handled by the invoked program; a failure to execute the shell itself produces an `Error running program ...` diagnostic from the child. Program output and termination are handled by the normal exec machinery.

## Example

Restore the default shell invocation and run a command:

```text
/set shell /bin/sh
/set shell_flags -c
/exec echo hello
```

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration and `-c` default.
- [source/exec.c](../../source/exec.c): `start_process` passes the flags as one `execl` argument, substitutes an empty string when unset, and bypasses this path for direct execution.
