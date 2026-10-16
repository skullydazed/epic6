# window log

## Synopsis

```text
/window [<window>] log [on|off|toggle]
```

## Description

Control whether output sent to the selected window is written to an external logfile. With no window selector, use the current window. New windows start with logging off. `on` starts logging, `off` closes the log, and `toggle` reverses the setting. Without an argument, report the current setting.

## Filename

When opening a log, EPIC chooses its filename in this order:

1. The [window logfile](logfile.md) value, if set, used without a suffix.
2. Otherwise, [set logfile](../set/logfile.md), followed by a dot and the current channel name, current query target, or `Window_<refnum>`, in that order of preference.

The compiled `/set logfile` default is `irc.log`, so examples include `irc.log.#chat` and `irc.log.Window_3`. If the global filename is unset, the suffix is still generated, beginning with a dot. The channel or query is chosen when the file opens; changing targets does not automatically rename an open log. Repeating `log on` while already logging does not reopen it under a new generated name.

The file is opened for append, preserving existing contents. EPIC writes dated start/end markers and flushes each logged line. It creates files with owner-only permissions and attempts to set an opened file's permissions to owner read/write. A failure to change permissions is reported but does not itself stop logging. Parent directories must already exist and be writable.

## What is logged

Logging records future output delivered to the window, including output while the window is hidden or held. It does not copy earlier scrollback into the file. It logs before display wrapping, so one output entry is not split merely because it spans several screen rows. Output suppressed before reaching the window logging stage is not recorded by this log.

The file is not necessarily an exact copy of terminal text. [window log_mangle](log_mangle.md) and [window log_rewrite](log_rewrite.md), with their global defaults, can transform it. Normal output rewriting occurs before window logging. This log is independent of the session log controlled by [set log](../set/log.md) and the configurable [log](../commands/log.md) facility.

The normal per-window logging path is bypassed by the simplified output path outside fullscreen mode. No IRC request is sent.

## Output and errors

Queries and changes initially report `Window LOG is ON` or `Window LOG is OFF`. An open failure then reports `Couldn't open logfile <filename>: <error>` and resets window logging to off, even if the initial report said on. An already open log reports `Logging is already on`. Successful opening reports `Starting logfile <filename>`; closing an open log reports `Logfile ended`.

Invalid values report `Value for LOG must be ON, OFF, or TOGGLE` and leave the setting unchanged. A query or invalid value stops subsequent operations in the same `/window` command. Additional words after a valid value are processed as subsequent window operations. `/window -log` is a no-op.

## Examples

```text
/window log on
/window 3 logfile chat.log log on
/window 3 log
/window 3 log off
```

Start logging the current window using its generated filename, log window 3 to `chat.log`, report window 3's setting, or stop its logging.

## Source references

- [source/window.c](../../source/window.c): `new_window`, `windowcmd_log`, `get_boolean`, and `windowcmd_logfile` implement defaults, filename choice, state changes, and explicit file changes.
- [source/log.c](../../source/log.c): `open_log`, `close_log`, `do_log`, and `add_to_log` implement append mode, permissions, markers, transformations, and writes.
- [source/screen.c](../../source/screen.c): `add_to_screen` and `add_to_window` define output suppression, rewriting, logging, and wrapping order.
- [include/config.h](../../include/config.h): defines the compiled global logfile name.
