# rename function

## Synopsis

`$rename(<oldpath> <newpath>)`

## Technical

Renames the resolved source path to the resolved destination using the operating system's `rename()` call. Both arguments are double-quoted words, so double quotes can group paths containing spaces regardless of `/xdebug dword`. Additional arguments are ignored.

Each path is normalized before the rename: tilde prefixes are expanded, relative paths use the client's working directory, and symbolic links and relative components are resolved through `realpath()`. Both the source and destination must therefore already exist. A nonexistent destination returns `-1` without calling `rename()`, even though the operating-system call itself normally permits a new destination name.

Normalization also means symbolic-link arguments operate on their resolved targets, rather than renaming or replacing the supplied link entry. This differs from passing the original paths directly to the system call.

## Practical

Use this function only when its existing-destination behavior is appropriate. It does not provide a general move-to-new-filename operation. In particular, `$rename(~/.ircrc ~/.ircrc.bak)` fails if the backup pathname does not already exist.

For resolved paths on the same filesystem, the system call can move an entry between directories and replace a compatible existing destination. Cross-filesystem moves generally fail; there is no copy-and-delete fallback. Permissions and directory constraints follow the [POSIX rename interface](https://pubs.opengroup.org/onlinepubs/9799919799/functions/rename.html).

## Returns

`0` if the operating-system rename succeeds; `-1` if either supplied path cannot be normalized or the rename fails. Missing source input returns the empty string. Missing destination input returns empty only after successful source normalization; an invalid source can return `-1` first. The wrapper supplies no separate diagnostic or errno value.

The operation changes the filesystem when successful and requires no IRC connection. There are no options.

## Examples

`$rename("/tmp/old name.txt" "/tmp/new name.txt")` accepts both names containing spaces. Both paths must already exist; on success, the resolved destination is replaced by the resolved source.

`$rename(~/.ircrc ~/.ircrc.bak)` returns `-1` if `.ircrc.bak` is absent, rather than creating a new backup filename.

## History

First appeared in EPIC2.

## Source

Registration and `function_rename()`: [source/functions.c](../../source/functions.c). `normalize_filename()` and tilde expansion: [source/ircaux.c](../../source/ircaux.c). Double-quoted argument parsing: [include/functions.h](../../include/functions.h).
