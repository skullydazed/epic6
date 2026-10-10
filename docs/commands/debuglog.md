# debuglog command

## Synopsis

```text
/debuglog
/debuglog on
/debuglog off
```

## Description

`debuglog` controls a local diagnostic log used by EPIC's internal debugging calls, particularly display and window code. It can help investigate output problems without adding diagnostic messages to the display being debugged.

With no arguments, it reports `Debug log is ON` or `Debug log is OFF`. Logging starts off.

`on` opens `debug.log` in the EPIC process's current working directory for appending. An existing file is preserved; a missing file is created if permitted. It writes a `Log file opened` entry and reports `Debug log is ON (debug.log)`. If already enabled, it keeps the existing file open and reports the same status without opening it again.

`off` writes a `Log file closed` entry when logging is enabled, closes the file, and reports `Debug log is OFF`. Repeating it while logging is off is harmless.

The command words are case-insensitive and must be complete: abbreviations such as `o` do not select an operation. Unknown words are silently ignored. Only the first argument is interpreted; additional arguments are ignored.

## File behavior and restrictions

The filename is fixed. There are no path, category, size-limit, or rotation options. To select another directory, use [`cd`](cd.md) before enabling logging. Changing directories while the log is already open does not move it; turn logging off and on to open `debug.log` in the new directory.

Entries contain local date/time, a milliseconds field, and the diagnostic text. Each entry ends with a newline and is flushed immediately. The log contains whatever the current source's `debuglog()` calls record; it is not a complete IRC traffic log or a transcript of every command.

If opening the file fails, EPIC reports `Cannot open debug.log. help!` and leaves logging off. The implementation does not report later write, flush, or close failures through this command, so an ON status alone does not prove every entry reached storage.

The command is available in every build, needs no server connection, and sends no IRC messages. There is no special configure-time flag required by its registration or implementation.

## Example

Enable logging, reproduce the issue, then close the log:

```text
/debuglog on
/debuglog off
```

Inspect `debug.log` in the directory where logging was enabled. Subsequent sessions append when enabled again.

## Source

See [source/debuglog.c](../../source/debuglog.c) for `debuglogcmd()`, file handling, and timestamped `debuglog()` output. It is included by [source/commands.c](../../source/commands.c), which registers the command. [source/window.c](../../source/window.c), [source/screen.c](../../source/screen.c), and [source/term.c](../../source/term.c) contain display-related logging calls.
