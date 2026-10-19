# set logfile

## Synopsis

```text
/set logfile [<filename>]
/set -logfile
```

Default: `irc.log`. With no filename, [set](../commands/set.md) displays the current value. `/set -logfile` unsets the destination and stops the global session log if it is open.

## Description

Selects the file used by [set log](log.md) for global session output. While that log is closed, changing the filename stores a choice for the next attempt to enable logging; it does not open a file by itself.

While the global file is open, assigning a filename closes the old file and immediately attempts to open the new one. Assigning the same filename also closes and reopens it. Unsetting the value reports `Unsetting LOGFILE turns off logging` and turns `log` off.

Per-window [window logfile](../window/logfile.md) destinations and selective [log](../commands/log.md) objects are separate logs, not overrides that redirect the global file. They can operate alongside it and record overlapping output.

## Paths and file handling

The filename is normalized when opening, including tilde expansion and relative-path resolution from the client's working directory. Parent directories are not created automatically. The string can contain spaces; the open routine also removes a matching pair of surrounding double quotes when the string contains a nonempty filename between them.

Files are opened in append mode, preserving existing contents. EPIC attempts to set permissions to owner read/write, including on an existing file. Successful opens and closes write dated start/end markers and report the file transition locally. Failure to normalize a path produces a warning and an attempt to use the supplied name; failure to open reports the filename and system error.

If a path change fails while logging was open, the previous file has already been closed and is not automatically restored. In that callback, the stored `log` boolean is not reset on open failure, so it can still say on even though no global logfile is open. After fixing the path, explicitly use `/set log on` to attempt opening it again. In contrast, a failed open initiated by `set log on` resets that boolean to off.

Changing the setting affects subsequent file output, does not move existing log contents, and sends no IRC request.

## Example

Choose a destination and start global logging:

```text
/set logfile ~/irc-session.log
/set log on
```

Change `logfile` while logging is open to switch files. To stop and remove the configured destination:

```text
/set -logfile
```

## Source references

- [source/log.c](../../source/log.c): `set_logfile` closes/reopens an active destination; `logger` handles explicit logging activation; `open_log` and `close_log` implement append mode, path normalization, permissions, and markers.
- [source/output.c](../../source/output.c): `put_echo` writes the global log before routing to windows.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, callback, unset handling, and default `irc.log`.
