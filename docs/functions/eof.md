# $eof()

## Synopsis

`$eof(<file handle>)`

## Technical

Tests the end-of-file indicator for a registered EPIC file handle, such as one returned by [`$open()`](open.md). This is an EPIC handle, not an operating-system file descriptor. Stream handles returned by [`$exec()`](exec.md) use the same handle registry.

Only the first function argument is converted to an integer; additional arguments are ignored. The function reads stream state without attempting a read or moving the file position.

For ordinary streams, the indicator is set when a read attempts to encounter end-of-file. Merely being positioned after the last byte does not necessarily set it. A newline-terminated final line can be returned while the indicator is still zero; another read is needed to encounter the end. [`$read()`](read.md) clears the ordinary stream's existing EOF/error indicators before its next read, so the value reflects that read's outcome rather than a permanent finished state.

## Practical

Use this function after reading an ordinary file to determine whether the end has been encountered. An empty `$read()` result can also represent a blank line or a read error; [`$error()`](error.md) checks an ordinary stream's error indicator separately. EOF is not a test for whether data is ready to read, so a zero result on a pipe does not mean the next read will finish immediately.

## Returns

| Value | Meaning |
| --- | --- |
| `-1` | No registered handle with that number, including an already-closed handle. |
| `0` | The stream's EOF indicator is not set. |
| Positive integer | The EOF indicator is set. For ordinary streams, the C library's nonzero `feof()` value is returned without normalizing it to `1`. |
| Empty string | No argument was supplied. |

## Restrictions

For a libarchive-backed file, the function returns EPIC's stored archive EOF flag. The current archive paths used by `$read()` and `$readb()` do not update this flag when they exhaust the entry, so `$eof()` can remain `0` after those reads reach the end. Do not rely on it to terminate an archive-reading loop. Archive support is conditional on libarchive; the function itself is always registered.

## Examples

```irc
@ at_end = eof($handle)
```

With a valid ordinary input handle, test `at_end > 0` for EOF. `at_end == 0` means the indicator is clear, while `-1` means the handle is invalid. Close finished handles with [`$close()`](close.md).

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). Handle lookup and reading are in [source/files.c](../../source/files.c); ordinary and archive EOF handling is in [source/elf.c](../../source/elf.c).
