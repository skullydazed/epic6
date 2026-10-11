# ftime function

## Synopsis

`$ftime(<file>)`

`$ftime("" <literal filename>)`

## Technical

Returns the last modification time (`mtime`) of a filesystem object, in whole seconds since the Unix epoch. It uses the object's `stat()` modification timestamp, rather than its access time, metadata-change time, or creation time. Fractional seconds are not returned, and the integer itself has no local-time formatting.

The filename is normally one extended word: double quotes group a path containing spaces and are removed before lookup. This parsing is independent of `/xdebug dword`, and additional arguments are ignored. If the first argument is an explicitly empty quoted word, all remaining text is used literally as the filename; do not surround that remaining text with quotes merely to group its spaces.

Tilde expansion supports `~/file` and `~user/file`. Relative paths use EPIC's current working directory, which [/cd](../commands/cd.md) can change. The filename is canonicalized with `realpath()` before `stat()`, so symbolic links report their targets' timestamps and dangling links fail. Directories and other filesystem objects can also have modification timestamps.

## Returns

The modification timestamp as an integer string. Missing input, an empty filename, path normalization failure, or `stat()` failure returns the empty string. The function does not open the file or separately test read access.

## Practical and examples

Use [$stime()](stime.md) to turn a successful timestamp into readable local-time text:

```irc
@ modified = ftime(/usr/local/bin/irc)
if (strlen($modified)) {echo $stime($modified)}
```

The displayed date depends on that file's timestamp and the current timezone. Checking for a nonempty timestamp before formatting avoids interpreting a failed lookup as a time value.

`$ftime("my file.txt")` and `$ftime("" my file.txt)` both look up a filename containing a space. `$ftime()` returns the empty string.

This checks filesystem paths, without searching EPIC's script load path. Archive-member pseudo-paths cannot pass filesystem path normalization. A timestamp is a snapshot of filesystem metadata, not a guarantee that the contents remain unchanged after the call.

## Source

Registration, argument parsing, and direct `stat()` lookup: [source/functions.c](../../source/functions.c). Tilde expansion and path normalization: [source/ircaux.c](../../source/ircaux.c). Extended-word parsing: [include/words.h](../../include/words.h).
