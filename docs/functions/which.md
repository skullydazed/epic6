# which

Find an existing filesystem path using a directory search list.

## Synopsis

```irc
$which(<file> [<directory>:<directory>:...])
```

## Technical

The filename always honors double-quoted grouping, independently of `/xdebug dword`. The complete remaining text, if present, is the colon-separated search path. An explicit path replaces the `load_path` setting; it is not an additional fallback after that setting. The search-path text itself is not dequoted, so directories containing spaces can be written directly without enclosing quotes.

If the filename contains `/`, EPIC first tries it directly, relative to the current directory when appropriate. If that fails, or the filename has no slash, it searches the directories in order. Each candidate is expanded for a leading `~` or `~user`, then resolved with `realpath`; the first existing candidate is returned as a canonical absolute path, resolving symbolic links. A bare filename is not automatically tried in the current directory unless the search path includes that directory.

Even a directly specified filename requires a non-null search path. With no explicit search path, an unset `load_path` therefore causes failure. Empty colon-separated path components produce a candidate beginning with `/`, so they search the filesystem root rather than the current directory; use `.` to search the current directory.

## Practical

Use this function to locate a script or other file when its exact location is unknown. It only checks existence: a directory can match, and a successful result does not establish readability or suitability for [load](../commands/load.md) or [stub](../commands/stub.md).

Unlike the [which command](../commands/which.md), this function does not open, read, decompress, or execute the candidate. It does not try compressed suffixes automatically. It also does not search the shell's `PATH` or require an executable file.

## Returns

The canonical path of the first existing candidate, or an empty string when the filename is missing, no candidate can be resolved, or no search path is available. There are no options or IRC connection requirements.

## Examples

```irc
$which(some_file)             /* Search the configured load_path. */
$which(some_file /bin:/usr/bin) /* Search only these directories. */
$which(some_file .)           /* Search the current directory. */
$which("my script" ~/scripts) /* A filename containing a space. */
```

## Source

Registered and implemented as `function_which` in [source/functions.c](../../source/functions.c). Search, canonicalization, and existence checking use `path_search`, `normalize_filename`, and `file_exists` in [source/ircaux.c](../../source/ircaux.c).
