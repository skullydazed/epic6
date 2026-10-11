# fsize function

## Synopsis

`$fsize(<file>)`

## Technical

Returns a filesystem object's size in bytes. The argument is a filename, rather than an EPIC file handle. It is parsed as one extended word: surround a filename containing spaces with double quotes. Additional arguments are ignored, and double-quoted words are recognized independently of the function word settings.

Tilde expansion supports `~/file` and `~user/file`. Relative paths use EPIC's current working directory, which [/cd](../commands/cd.md) can change. The path is canonicalized with `realpath()`, resolving symbolic links and `.` or `..` components, then its `stat()` size is returned. For a symbolic link, this is the target's size; a dangling link fails.

Missing input, an empty filename, path normalization failure, or `stat()` failure returns `-1`. An empty quoted first argument does not cause the remaining text to be used as a literal filename.

The function does not require the object to be a regular file. For directories or special objects, it returns the operating system's `st_size` value, whose meaning depends on the object type; a directory's size is not the total size of its contents.

## Practical

Use this function to distinguish an existing zero-length file from a failed lookup. It does not open or read the file and performs no separate read-access check, so a reported size does not guarantee that [$open()](open.md) or [/load](../commands/load.md) can read it. Lookup may still fail if permissions prevent resolving or inspecting the path.

See [$fexist()](fexist.md) for a path read-access test and [$ftell()](ftell.md) for the current position of an open stream. This function does not search EPIC's script load path. Archive-member pseudo-paths cannot pass the required filesystem path normalization, even when libarchive support is available.

## Returns

The object's byte size as an integer string, including `0` for an empty regular file, or `-1` on failure. The value describes the object when inspected; it can change afterward.

## Examples

| Expression | Meaning |
| --- | --- |
| `$fsize(~/.ircrc)` | Size of your startup file, or `-1` if lookup fails. |
| `$fsize("my file.txt")` | Size of one filename containing a space. |
| `$fsize()` | `-1`. |

## History

First appeared in EPIC3pre7.

## Source

Registration and filename parsing: [source/functions.c](../../source/functions.c). Tilde expansion, path normalization, and `file_size()`: [source/ircaux.c](../../source/ircaux.c). Its `epic_stat()` wrapper is in [source/elf.c](../../source/elf.c). Extended-word parsing: [include/words.h](../../include/words.h).
