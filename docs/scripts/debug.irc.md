# debug.irc

## Synopsis

```text
/load debug.irc
/debug_section
/debug_endsection
/call.time <command>
/call.trace <debug-level> <command>
/call.xtrace <category>[,<category>...] <command>
```

## Description

This script demonstrates how to debug a section of a large script while other scripts remain loaded. Put `debug_section` immediately before the code to trace and `debug_endsection` immediately after it, inside the alias or hook that actually runs the code. Putting the markers around a definition during loading traces the definition's loading instead of its later execution.

These are script aliases over EPIC's logging, debugging, timing, and stack facilities. Loading the file defines the helpers but does not itself begin tracing. There are no script-specific settings.

## Section tracing and files

`debug_section` records the current global `log` value in `logstate`. If logging is already on, it also saves `logfile` in `logfile_old` and stops that log. It then selects `epic.dbg`, turns global logging on, and sets [debug](../set/debug.md) to `7`, enabling statement, expansion, and function tracing.

`epic.dbg` is relative to the client's current working directory when logging opens it. It is opened for append, not truncated, and records other output passing through the global session log during the traced interval too. An unwritable path or other open error is reported by the logging implementation and leaves logging off; the alias still proceeds to enable debugging.

`debug_endsection` sets debugging to `0`, stops logging, assigns the saved logfile name, and restores the saved on/off state. It does not restore a previous nonzero debug level.

The restoration has important limits:

- The previous logfile is saved only when logging was on. If logging was off, ending the section uses an unset or stale `logfile_old` rather than reliably restoring the original filename.
- `logstate` and `logfile_old` are ordinary global assignments, not a stack. Nested or overlapping sections overwrite each other's saved state.
- Logging changes made inside a section are replaced by the saved values at its end. An early return or other path that skips `debug_endsection` leaves the tracing state active.

Use these markers in a controlled section and ensure its end marker runs. See [log](../set/log.md) and [logfile](../set/logfile.md) for the underlying file behavior.

## Timing a command

`call.time` samples [utime](../functions/utime.md) before and after executing the supplied command, computes elapsed seconds, and formats the result with six decimal places through `trunc`. It temporarily enables `floating_point_math` for the calculation and restores that setting with `stack`.

As a command it displays a human-readable elapsed time followed by the command text. As `$call.time(<command>)` it returns the numeric elapsed-seconds string instead; output from the command being measured is not suppressed. The measurement covers the command's synchronous execution. Scheduling a timer or sending a network request does not make it wait for the eventual timer callback or server response. The timestamp source is wall-clock time, so clock adjustments can affect the result.

## Tracing one command

`call.trace` saves the `debug` and `output_rewrite` settings, temporarily clears the output rewrite, and enables the supplied numeric debug mask while executing the command. It also saves and removes installed `window`, `yell`, and `status_update` hook lists. It then restores the saved settings and hooks. For example, `7` enables all three ordinary script trace classes; `0` disables those classes for the command.

`call.xtrace` instead accepts comma-separated [xdebug](../commands/xdebug.md) categories, including `+` or `-` prefixes, and saves their prior states before applying them. It saves `output_rewrite` but does not clear its value. Its `stack push on` operations temporarily remove the same three installed hook lists, restoring them afterward. It does not change the ordinary numeric `debug` setting or start a logfile.

Stack restoration replaces changes to those saved settings or hook lists made by the traced command. As with other manual push/pop wrappers, interrupted execution can prevent restoration. Category lookup in `call.xtrace` uses wildcard matching through `xdebug`; prefer explicit valid category names. These wrappers provide diagnostic output, not a separate structured success result.

## Examples

Trace a small alias body to the section log:

```text
alias debug_demo {
    debug_section;
    echo Inspect this section;
    debug_endsection;
};
```

Time or trace a command directly:

```text
/call.time echo Timing example
/call.trace 7 echo Tracing example
/echo $call.time(echo Measured command)
```

The function example prints the measured command's own output, then the elapsed-seconds value. Diagnostic arguments and expanded values can appear in the output and log; choose the section accordingly.

## Source references

- [script/debug.irc](../../script/debug.irc): all five helper aliases and their saved state.
- [source/log.c](../../source/log.c): append-mode logging, path handling, and open failures.
- [source/functions.c](../../source/functions.c): `utime`, `trunc`, `tdiff`, and function diagnostics.
- [source/commands.c](../../source/commands.c), [source/alias.c](../../source/alias.c), [source/hook.c](../../source/hook.c), and [source/vars.c](../../source/vars.c): statement tracing, saved settings, and hook-list stacks.
- [source/debug.c](../../source/debug.c): named `xdebug` categories and lookup.
