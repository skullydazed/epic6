# window logfile

## Synopsis

```text
/window [<window>] logfile [<filename>]
/window [<window>] -logfile
```

## Description

Set the name of the logfile used by the selected window. With no window selector, use the current window. When [window log](log.md) is on, output is appended to this file. Setting a filename while logging is off does not open the file or enable logging.

New windows have no explicit filename. When `window log on` opens a log without one, it uses [set logfile](../set/logfile.md) followed by a dot and the current channel, query target, or `Window_<refnum>`. An explicit `window logfile` value replaces that generated name entirely; no suffix is added.

The filename argument is consumed as one word. Use filenames without spaces. Following words are processed as subsequent window operations. The command stores the filename without checking whether it can be opened until an open is actually attempted.

## Active logging and queries

Setting a filename while the window has an open log closes the old file and opens the new file for append, with end/start markers. Setting the same filename also closes and reopens it.

Without an argument, report `Window LOGFILE is <filename>` or `Window LOGFILE is not set.` **A query also affects an open log:** when an explicit filename exists, it closes and reopens that file; when logging uses a generated filename, it closes the log instead. The latter reports `Unsetting WINDOW LOGFILE turns off logging`.

`/window -logfile` clears the explicit filename and reports `Window LOGFILE unset`. In the current implementation this branch returns immediately, leaving any already open file logging under its old name. The next fresh `window log on` uses the generated filename once that file has been closed.

If reopening fails, EPIC reports `Couldn't open logfile <filename>: <error>` and leaves no file open. The stored filename is retained, and this handler does not reset the window's separate logging flag, so `window log` can still report on even though output is no longer being written. Use `window log off` followed by `window log on` to reconcile the state and retry.

## File behavior

Opening appends rather than overwrites, creates no missing parent directories, and requires filesystem permission. EPIC attempts filename normalization; if that fails, it warns and tries the original name. It creates files with owner-only permissions and attempts to set opened files to owner read/write. A permission-change warning does not itself stop logging.

Changing the filename does not copy earlier scrollback or old log contents to the new file. It works for invisible windows, does not change their visibility, and sends no IRC request. See [window log](log.md) for logged output and formatting.

## Examples

```text
/window 3 logfile chat.log log on
/window 3 logfile archive.log
/window 3 log off -logfile log on
```

Log window 3 to `chat.log`, switch its active log to `archive.log`, or close it and restart using the generated filename.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_logfile` implements assignment, query reopen behavior, and the early-return unset branch; `new_window` and `windowcmd_log` define defaults, generated names, and logging-state reconciliation.
- [source/log.c](../../source/log.c): `open_log`, `close_log`, and `do_log` implement append mode, markers, permissions, and open errors.
- [source/ircaux.c](../../source/ircaux.c): `normalize_filename` implements pathname normalization.
