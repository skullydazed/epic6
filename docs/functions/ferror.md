# ferror function

## Synopsis

`$ferror(<file handle>)`

## Technical

Returns the error indicator for an ordinary file stream identified by an EPIC file handle from [$open()](open.md) or [$exec()](exec.md). This is a stream status, rather than an operating system error number or message. Use EPIC's handle, not an operating system file descriptor.

[$read()](read.md) and [$readb()](readb.md) clear the ordinary stream's error and end-of-file indicators before reading. Check `$ferror()` immediately after the operation you want to inspect, before another read clears its status. Other operations, including writes, can also set the stream's error indicator. Calling `$ferror()` does not clear it.

An empty `$read()` result can represent a blank line, end of file, or an error. `$ferror()` checks for a stream error; [$eof()](eof.md) checks for end of file. Neither function reports whether a pipe read will block.

The first argument word is converted to an integer; additional words are ignored. Missing input returns the empty string.

## Returns

| Result | Meaning |
| --- | --- |
| `-1` | The handle is not registered, including a handle that has been closed. |
| `0` | The stream's error indicator is clear. |
| Positive integer | The stream's error indicator is set. The value is not necessarily `1`. |

## Restrictions

With libarchive support, `$open()` can return archive-backed handles that have no ordinary C file stream. The current `$ferror()` implementation does not support these handles: it passes a null stream pointer to `ferror()`, which can crash EPIC. Use this function only for ordinary file or pipe streams.

## Examples

After reading from a known ordinary stream:

```irc
@ line = read($handle)
@ read_error = ferror($handle)
if (read_error > 0) {echo The read encountered a stream error}
```

Check that the handle opened successfully before reading. A `0` error indicator alone does not establish that data was read; the returned line and EOF status must also be considered.

## Source

Registration and argument parsing: [source/functions.c](../../source/functions.c). Stream lookup, error querying, and indicator clearing before reads: [source/files.c](../../source/files.c). Ordinary and archive-backed stream construction: [source/elf.c](../../source/elf.c).
