# loadformats

## Synopsis

```text
/load loadformats
/addformat <event> <value>
/delformat <event>
/dumpformats
/loadformats <filename>
/saveformats <filename>
/set format_<event> [<value>]
```

## Description

This script is a front end to EPIC's implied [on](../commands/on.md) hooks. It creates dynamic `format_<event>` string settings that control event display without requiring an explicit display handler for each event. The hook expands the setting when the event occurs and formats it through [cparse](../functions/cparse.md), so changing the setting changes subsequent output.

Use a valid current event name, such as `public` or `msg`, and the positional arguments documented for that event. An implied hook runs after ordinary handlers permit default processing. Its formatted output then replaces the C default display. An earlier suppressing hook, or a protocol path that never reaches that event, prevents this implied formatting from running. Merely defining a format does not generate events or send IRC commands.

There are no formats or default colors installed just by loading this file. It defines the five command aliases above and uses the current C `hookctl` and `symbolctl` facilities directly, without requiring `addset`.

## Commands

`addformat` uppercases the event identifier, installs an implied hook referring to `$format_<event>`, creates that script-defined string setting, assigns the supplied value, and prints `Added format for the ... event`. A missing or empty value does nothing; it is not an optional query or a way to install an empty format. Repeating it can replace an existing implied hook and setting value.

`delformat` removes the event's implied hook and deletes its `format_<event>` setting. Missing event text prints usage. It does not remove ordinary explicit `on` handlers for that event.

`dumpformats` checks all event lists and invokes `delformat` for every nonempty implied hook. Its scope is not limited to hooks originally installed by this script; it can delete implied hooks belonging to other configuration code too. The actual command name is plural, not `dumpformat`.

The aliases do not verify each control-function return before printing their status messages. Invalid event names or symbol conflicts can therefore leave a setting without a working implied hook or produce a misleading success message. Use valid event names and avoid colliding `format_*` symbols. Settings created this way are script settings, despite the low-level `builtin_variable` name used by `symbolctl`.

## Format files

A format file has one definition per line:

```text
<event> <format text>
```

For example:

```text
public %K<%n$0%K>%n $2-
msg %K[%C$0%K]%n $1-
```

The first word selects the event and the remaining text is its value. `%` sequences are `cparse` color/style directives; dollar expandos use that event's arguments. Quotes, backslashes, and embedded function calls participate in EPIC expansion and `cparse` parsing, so do not assume arbitrary quoted text is copied unchanged. Formatting expressions can call functions; use definitions whose expansion you intend.

`loadformats` opens the supplied file for reading and calls `addformat` for each line. It does not search `load_path`, clear previous formats first, or implement a comment syntax. Empty values are skipped by `addformat`, but a comment line containing words is not specially ignored. Paths are local and ordinarily relative to the client's current directory. Substituted filenames are not quoted in the underlying `open` calls, so names containing spaces are unreliable.

`saveformats` enumerates all nonempty implied hooks and writes `<event> <value>` records using the corresponding `format_<event>` settings. It does not serialize each arbitrary implied-hook body. A hook installed by some other mechanism without that setting cannot be faithfully saved this way. The exporter also uses `eval` to construct its writes; complex expansion or quoting can prevent a lossless round trip.

EPIC's `open(... W)` mode is append, including the lowercase `w` used here. Saving therefore creates or appends to the file rather than replacing it; repeated saves accumulate duplicate definitions. On a later load, later records for the same event replace earlier ones. Neither command provides a backup or transactional rollback.

Missing filenames print usage. An open failure reports `ERROR: Unable to open file for reading: ...` or the corresponding writing error. Individual read/write/close results are not checked, and none of these command aliases defines a function return value or a verified count of successful definitions.

## Example

Put the two example definitions above in `myformats.txt`, then run:

```text
/load loadformats
/loadformats myformats.txt
/set format_public
/delformat public
```

This loads the definitions, queries the current public-message format, then removes that implied format and its setting. Normal explicit handlers can still take precedence throughout.

## History

Written by fudd (Brian Weiss); first included in EPIC5-0.0.5.

## Source references

- [script/loadformats](../../script/loadformats): five aliases, dynamic settings, import/export, and broad deletion scope.
- [source/hook.c](../../source/hook.c): implied-hook installation, expansion through `cparse`, dispatch precedence, and default suppression.
- [source/alias.c](../../source/alias.c): script-setting creation and deletion through `symbolctl`.
- [source/functions.c](../../source/functions.c) and [source/files.c](../../source/files.c): append-mode `open`, text file reads/writes, and `cparse`.
