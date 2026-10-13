# realpath function

## Synopsis

`$realpath(<filepath>)`

## Technical

Returns the absolute path of an existing file or directory, resolving symbolic links, `.` and `..` components, and redundant separators through the operating system's `realpath()` function. All path components, including the final file or directory, must exist and be resolvable. The current implementation has no fallback that simply returns the supplied path when `realpath()` is unavailable. See the [POSIX realpath specification](https://pubs.opengroup.org/onlinepubs/9699919799/functions/realpath.html) for pathname resolution and platform errors.

The entire argument string is the path. There is no word splitting or removal of double quotes, so a path containing spaces should be supplied directly, without adding grouping quotes. Leading `~`, `~/`, or `~user/` is expanded using the client's home directory or the named user's home directory before resolution. Relative paths start from the client's current working directory; [/cd](../commands/cd.md) changes that directory.

## Practical

Use this function to obtain an absolute filename from a path containing relative components or symbolic links. It resolves filesystem paths, not load-path searches, wildcard patterns, or virtual archive-member paths.

## Returns

The resolved absolute path, without added quotes, or the empty string on failure. Missing input, an unknown user in tilde expansion, nonexistent path components, inaccessible directories, and symbolic-link loops can cause failure. The wrapper does not report a separate diagnostic or errno value. It neither creates files nor changes the working directory, and requires no IRC connection.

## Examples

If `/home` links to `/usr/home`, `/usr/local` links to `/local`, and `/local/src` exists:

`$realpath(/home/../local/src)` returns `/local/src`.

Resolution follows `/home` to `/usr/home`, then `..` to `/usr`, then `local` to `/local`, and finally `src`.

`$realpath(.)` returns the client's current directory as an absolute path.

## Source

Registration and `function_realpath()`: [source/functions.c](../../source/functions.c). `normalize_filename()` and `expand_twiddle()`: [source/ircaux.c](../../source/ircaux.c).
