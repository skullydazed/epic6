# set log

## Synopsis

```text
/set log [on|off|toggle]
```

Default: `off`. With no value, [set](../commands/set.md) displays the current value. `toggle` reverses it; other values are rejected as invalid boolean settings.

## Description

Enables or disables the global session log at the filename selected by [logfile](logfile.md), whose default is `irc.log`. It records new output passing through the client's normal output entry point, rather than saving the existing lastlog buffer or filtering entries through [lastlog_level](lastlog_level.md).

Output from multiple windows shares this file without automatic per-window labels, because global logging occurs before window routing. Use [window log](../window/log.md) or the separate [log](../commands/log.md) command's selective logs when you need window-specific or filtered logging.

## File handling and errors

Turning logging on opens the file in append mode. Existing contents are retained. Tilde and relative filenames are normalized; a relative filename is resolved from the client's current directory. Parent directories must already exist and be writable.

A successful open writes a dated `IRC log started ...` marker and reports `Starting logfile ...`. The client attempts to restrict file permissions to owner read/write, including an existing file. Turning logging off writes an `IRC log ended ...` marker, closes the file, and reports `Logfile ended`.

If `logfile` is unset, enabling logging reports `You must set the LOGFILE variable first!` and returns this setting to off. An open failure reports the filename and system error and also returns it to off. Turning logging off remains possible regardless of the configured filename.

Changing `logfile` while a file is open closes it and attempts to open the new destination; assigning the same path closes and reopens it. Unsetting `logfile` stops logging. See that setting for its own failure behavior.

## Output scope

Global logging happens before [on window](../on/window.md) filtering, window rewriting, and display wrapping. Suppressing a line at that hook does not erase its global log copy. Output suppressed earlier, before reaching the normal output entry point, is not recorded by this path.

[Log_rewrite](log_rewrite.md) and log mangling can alter the recorded text. Calls that explicitly inhibit logging, such as `xecho -nolog`, can bypass it. Already stored lastlog entries are not dumped when logging starts; use [lastlog](../commands/lastlog.md)'s file output for recalling retained history.

This controls local files and sends no IRC request.

## Example

```text
/set logfile ~/irc-session.log
/set log on
```

Append subsequent session output to that file. Stop with `/set log off`.

## Source references

- [source/log.c](../../source/log.c): `logger`, `open_log`, `close_log`, `set_logfile`, and `add_to_log` implement state, append mode, permissions, markers, rewriting, and write flushing.
- [source/output.c](../../source/output.c): `put_echo` writes the global session log before calling `add_to_screen`.
- [source/screen.c](../../source/screen.c): window routing and window-level filtering happen later.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration and default off, with default filename `irc.log`.
