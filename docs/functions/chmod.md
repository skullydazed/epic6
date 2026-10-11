# chmod function

## Synopsis

```text
$chmod(filename octal-mode)
```

## Technical

`chmod()` changes access permission bits on one file or directory through the Unix `chmod()` system call. The filename comes first and is always parsed as a double-quote-aware word, so a path containing spaces can be quoted independently of [`xdebug dword`](../commands/xdebug.md). The mode is the next function argument word; further words are ignored.

The mode is converted in base 8, so `600` and `0600` both mean owner read/write with no group or other permissions. Symbolic modes such as `u+w` are not supported. The parser does not validate the entire mode: a nonnumeric string converts to zero, and an octal prefix can be accepted before invalid trailing characters. Supply a valid octal permission value.

Pathnames undergo tilde expansion and `realpath()` resolution before the system call. Relative paths use the client's working directory, which [`cd`](../commands/cd.md) can change. The target must exist. Symbolic links are resolved to their targets; there is no option to change a link itself, recurse through directories, expand globs, or process multiple files.

## Practical

Use the pathname form to set permissions on a known file. The client process must own the target or have the privileges required by the operating system. Filesystem restrictions and platform handling of special permission bits also apply. A discussion of Unix permissions is available in the system's `chmod` manual.

This function changes permissions immediately when evaluated; it is not merely a query. It does not create the file and does not return a descriptive error message.

## Returns

| Result | Meaning |
| --- | --- |
| `0` | The system call succeeded |
| `-1` | Path normalization or the system call failed |
| Empty string | A required argument is missing, or the numeric-target branch rejects its file ID |

## Restrictions

A first argument that converts completely with base-detected `strtoul()` is treated as a numeric file ID rather than a pathname. This branch checks whether that ID belongs to an EPIC open-file record, then passes the number directly to the operating system's `fchmod()`.

EPIC file IDs are sequential identifiers and need not equal operating-system descriptors. The current numeric branch does not translate between them, so it can fail or affect a different open descriptor. Prefer a pathname; to address a file named only with digits, use a path such as `./123` so it is not interpreted as an ID. Quoting the digits alone does not prevent numeric interpretation.

## Examples

```text
$chmod(./notes.txt 600)
$chmod("./shared notes.txt" 640)
$chmod(./123 644)
```

These set the complete permission mode rather than adding permissions to the existing mode. Each returns `0` on success or `-1` on failure.

## Source

[source/functions.c](../../source/functions.c) registers `chmod` and implements `function_chmod()`. [source/ircaux.c](../../source/ircaux.c) normalizes paths, and [source/files.c](../../source/files.c) defines EPIC file IDs and validity lookup. The [POSIX chmod specification](https://pubs.opengroup.org/onlinepubs/9799919799/functions/chmod.html) describes operating-system permissions and errors.
