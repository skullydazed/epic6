# $rmdir()

## Synopsis

`$rmdir(<directory> [<directory> ...])`

## Technical

Removes the specified directories from the filesystem using the operating system's `rmdir()` call. Each directory must be empty and removable under the operating system's permission and filesystem rules. The function does not recursively remove contents.

Directory arguments are double-quoted words: double quotes group paths containing spaces regardless of `/xdebug dword`. Tilde expansion is permitted. Relative paths use the client's working directory, which [/cd](../commands/cd.md) can change.

Before removal, each path is normalized through `realpath()`, resolving symbolic links and relative components. A symbolic link to a directory therefore selects its resolved target directory for removal; it does not remove the link itself. A nonexistent or otherwise unresolvable path counts as a failure without calling `rmdir()`.

## Practical

A script can use this function to remove empty temporary directories created during installation or cleanup. Each path is attempted in order, even if an earlier path fails. Successful removals are not undone when another path fails.

## Returns

- `0` when every attempted removal succeeds, or when no arguments are supplied.
- A positive integer counting paths that could not be normalized or removed.

The wrapper does not identify which paths failed or return their individual operating-system error codes. To associate a result with a path, make separate calls. There are no options and no IRC connection is required.

An empty quoted argument is not handled as an independent empty pathname: the wrapper instead treats the remaining argument text as one final pathname and stops parsing. Avoid empty arguments.

## Examples

`$rmdir(~/temp)` returns `0` if the resolved directory exists, is empty, and can be removed; otherwise it returns `1`.

`$rmdir("/tmp/first directory" "/tmp/second directory")` attempts both paths and returns `0`, `1`, or `2`, according to how many fail.

`$rmdir()` returns `0` without changing the filesystem.

Registration and `function_rmdir()`: [source/functions.c](../../source/functions.c). Path normalization and tilde expansion: [source/ircaux.c](../../source/ircaux.c). Double-quoted word parsing: [include/words.h](../../include/words.h).
