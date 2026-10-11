# $dbmctl()

## Synopsis

```text
$dbmctl(open <type> <filename>)
$dbmctl(open_read <type> <filename>)
$dbmctl(close <refnum>)
$dbmctl(add <refnum> "<key>" <value>)
$dbmctl(change <refnum> "<key>" <value>)
$dbmctl(delete <refnum> "<key>")
$dbmctl(read <refnum> "<key>")
$dbmctl(next_key <refnum> <start-over>)
$dbmctl(all_keys <refnum>)
$dbmctl(error <refnum>)
```

## Technical

Provides a low-level interface to persistent key/value files using EPIC's bundled SDBM implementation. It is always registered and does not require an external gdbm library. Database entries do not become script variables unless your script assigns their values.

Use `std` for `<type>`. The token is required but currently ignored. `<filename>` is the entire remaining input, used literally as a base name for two files, `<filename>.dir` and `<filename>.pag`. Do not add either suffix. Relative names use EPIC's working directory; no tilde expansion or filename dequoting is performed. Spaces in the remainder are part of the filename.

A successful open returns an EPIC database reference number starting at `0`. These handles are separate from [`$open()`](open.md) file handles and must be closed with `dbmctl(close ...)`, not [`$close()`](close.md).

Keys are always parsed as double-quoted words, independent of `/xdebug dword`. Quotes allow spaces in keys and are removed before lookup. Matching is exact and case-sensitive. Values are the whole remaining text, without another dequoting step; an empty value is allowed. Keys must be nonempty, and key plus value length cannot exceed 1008 bytes. Keys and values are stored as bytes, with no character-set conversion; script strings cannot carry embedded NUL bytes through these setters.

## About hash files

Hash files are binary files intended for storing script configuration and other data across client sessions. Their contents are not plain text for direct editing. Close databases when finished to release their file descriptors. The function does not promise durable storage after a system failure or coordinate concurrent writers.

## Options and returns

Operation names are case-insensitive. Use the full names; dispatch accepts various prefixes and longer names based on its fixed-length comparisons.

| Operation | Behavior and return |
| --- | --- |
| `open` | Open read/write, creating missing files without clearing existing data. Return a nonnegative handle, or `-1` with a diagnostic on failure. New files request mode `0660`, subject to the process umask. |
| `open_read` | **Currently behaves like `open`**, including read/write access and file creation. The earlier `open` prefix test captures this spelling before the read-only branch. |
| `close` | Close the database and remove its handle. Return `0`, or `-1` for an invalid/already-closed handle. |
| `add` | Insert a key only if absent. An existing key is left unchanged. Return `0` for success; see error limitations below. |
| `change` | Replace the value, or insert it if the key is absent. Return `0` for success; see error limitations below. |
| `delete` | Remove a key. Return `0` for success; see error limitations below. |
| `read` | Return the value. Missing keys, empty values, invalid handles, and failures can all return an empty string. |
| `next_key` | Any nonzero `<start-over>` starts at the first key; zero advances the database's existing iterator. Return a key, or empty at exhaustion, for an invalid handle, or on failure. |
| `all_keys` | Restart iteration and return all keys as an unquoted space-separated string. Keys containing spaces make this result ambiguous. The iterator is left exhausted. |
| `error` | Return the database's stored error value. For handle `-1`, return the saved errno from the most recent failed open; another invalid handle returns `-1`. |

Key iteration order is unspecified. After `all_keys`, restart `next_key` with a nonzero argument before another iteration. Large databases can take time and produce a large `all_keys` result. Getter, close, delete, and iteration arguments beyond those listed are ignored; open filenames and write values consume the remainder.

## Restrictions

Missing required arguments or an unrecognized operation return an empty string. Invalid handles for `add`, `change`, and `delete` return `-1`.

For a valid handle, failed writes or deletes return the database's stored error value rather than the operation's own failure status. That stored value starts at `0`, is set by certain I/O failures, and is not cleared after successful operations. A duplicate `add`, missing-key `delete`, empty key, or oversized pair can therefore return `0` even though the requested change was not made. `error` is not a reliable errno for the immediately preceding operation, and a nonzero stored error can be stale. Read back important changes rather than treating `0` alone as proof of success.

The `open_read` dispatch behavior means it cannot be used to enforce read-only access in the current implementation.

## Examples

```irc
@ database = dbmctl(open std ./preferences)
if (database >= 0) {
    @ result = dbmctl(change $database "display name" Example User)
    echo Stored name: $dbmctl(read $database "display name")
    @ result = dbmctl(close $database)
}
```

This creates or opens `preferences.dir` and `preferences.pag`, writes a value, reads it back, and closes the handle. A zero handle is valid, so test `>= 0` rather than truthiness.

## History

The function first appeared in EPIC5-0.0.8.

## Source

Registered and wrapped in [source/functions.c](../../source/functions.c). Dispatch and database handles are in [source/files.c](../../source/files.c); the storage format, size limits, and error handling are in [source/sdbm.c](../../source/sdbm.c) and [include/sdbm.h](../../include/sdbm.h).
