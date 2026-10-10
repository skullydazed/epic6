# log

Create and manage independent logfiles that copy selected display output to disk.

## Synopsis

```text
/log
/log new operation [arguments] ...
/log logfile-name-or-number operation [arguments] ...
```

## Description

`log` manages generalized logfiles independently of session and window logging. Several logfiles can overlap, so one displayed line can be written to several files. Selection uses output metadata and levels, not raw IRC traffic.

Each invocation starts without a selected logfile. `new` creates and selects one; a name, reference number, or `refnum` operation selects an existing one. Following operations use that selection within the invocation. A bare selection performs no implicit description. With no operations, the command lists registered logfiles. Operation names are case-insensitive and accept prefixes, optionally preceded by `-` or `/`; use full names to avoid ambiguity or collisions with logfile names.

A new logfile has a sequential reference number and the same number as its logical name. It is off, has no filename or targets, accepts all levels, uses target mode, and is associated with the current server context. Configure its type, targets, and filename, then turn it on.

## Operations

| Operation | Effect |
| --- | --- |
| `new` | Create and select a logfile. |
| `refnum name-or-number` | Select an existing logfile; it does not change its reference number. A bare name or number can also select it. |
| `list` | Display all registered logfiles and their brief configuration. |
| `describe` | Display the selected logfile's full configuration. |
| `name name` | Set a unique, case-insensitive logical name. `name -` removes it. Numeric selectors always mean reference numbers. |
| `filename path` | Set the destination filename. Without an argument, display it. Changing an active logfile closes it and leaves it off; use `on` again. |
| `type target\|window\|server` | Choose the selection domain and clear all targets, even when reselecting the same type. An invalid type also clears targets. |
| `add item[,item...]` | Add one comma-separated list of targets, windows, or server numbers according to the domain. |
| `remove item[,item...]` | Remove targets. Window and server removal have implementation limitations described below. |
| `server number\|all` | Set the associated server for target-mode filtering. `all` (internally `-2`) removes that restriction. |
| `level levels` | Set the accepted output-level mask. Invalid levels produce warnings. |
| `mangle types` | Configure output normalization. Without an argument, display the current rule. |
| `rewrite "template"` | Set the per-log rewrite template. With no argument, clear it so the global rewrite setting applies. |
| `on` | Open the file for append and start logging. Requires a filename and a successful open. |
| `off` | Close the file and stop logging, retaining the configuration. |
| `kill` | Close and unregister the logfile; it does not delete the disk file. |
| `activity` | Reset the activity timestamp to now. Matching output and on/off operations also update it. |

Most arguments are single whitespace-delimited words. `level`, `rewrite`, `server`, and `type` use quoted-word parsing; filename, name, add, remove, and mangle do not support multiword quoted arguments through this command. Keep comma-separated items in one word. Missing selections or arguments and invalid selectors produce diagnostics. Some failed operations clear the current selection, while others retain it; an invalid command token does not necessarily discard an existing selection.

## Domains and matching

A **target** logfile matches recorded channel or nickname targets exactly and case-insensitively, with its associated server and level mask also applying. Wildcards are not supported. An empty target list does not match ordinary targeted output, but can accept output with no recorded target. A nonempty list excludes targetless output.

A **window** logfile matches added window reference numbers, names, or UUIDs. Numeric entries can match either internal or user reference numbers. Only its level mask also applies; the associated server is ignored.

A **server** logfile matches added server reference numbers. `add all` accepts output from any server; setting `server all` alone is insufficient in this domain. It supports at most 32 reference slots. Duplicate server entries can occupy separate slots and cause duplicate writes for a line.

**Removal limitation:** `remove` works for target names. In window mode it does not remove the stored window-target entries. In server mode it converts the following comma-list remainder rather than the current item, so it may remove the wrong reference, commonly zero for a final item. To change those lists reliably, reset the type and re-add the desired entries, or create a replacement logfile.

## File output and rewriting

`on` appends rather than truncates, can create a new file, and writes a start timestamp. `off` and `kill` write an end timestamp when a file is open. The opener attempts owner-only read/write permissions (`0600`), including on an existing destination. It tries tilde expansion and path normalization; if normalization fails, it warns and uses the original spelling. In particular, a nonexistent tilde path can fail even though a plain new filename can be created.

A zero per-log mangle rule falls back to global log mangling. A missing per-log rewrite falls back to `/set log_rewrite`. Rewriting occurs after mangling, with `$0` containing the output window's user reference number and `$1-` the line text; that combined argument string is limited to 10,240 bytes. File lines are flushed after each write. Logging inhibition and recursive-log protection can suppress writes.

## Examples

Create a target logfile:

```text
/log new name channel_log type target server all add #epic filename epic.log on
/log channel_log describe
/log channel_log off
```

Create a server logfile for server reference zero:

```text
/log new name server_log type server add 0 filename server.log on
```

The command is always registered and does not require a server connection merely to configure or open files. Logging actual server output requires that output to reach the display path.

## History and source

`log` first appeared in EPIC4-1.1.8.

Registration is in [source/commands.c](../../source/commands.c). Operations, defaults, and matching are in [source/logfiles.c](../../source/logfiles.c). Opening, permissions, timestamps, mangling, and rewriting are in [source/log.c](../../source/log.c), with display integration in [source/screen.c](../../source/screen.c). Level parsing is in [source/levels.c](../../source/levels.c), and filename normalization is in [source/ircaux.c](../../source/ircaux.c).
