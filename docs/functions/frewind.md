# frewind function

## Synopsis

`$frewind(<file handle>)`

## Technical

Attempts to reset an ordinary file stream's position to the beginning, using an EPIC file handle returned by [$open()](open.md). After a successful reset, [$read()](read.md) reads the first line again and [$readb()](readb.md) reads bytes starting at the beginning.

The first argument word is converted to an integer; additional words are ignored. Missing input returns the empty string. This takes an EPIC file handle, not an operating system file descriptor.

The implementation calls `fseek(stream, 0, SEEK_SET)`, rather than the C `rewind()` function. A successful seek clears the end-of-file indicator, but this function does not explicitly clear the stream's error indicator. Its return value is not the [$ferror()](ferror.md) result.

## Returns

| Result | Meaning |
| --- | --- |
| `-1` | The handle is not registered, including a handle already closed. |
| `0` | The handle was found and a seek was attempted; the seek's result is discarded. |
| Empty string | No input was supplied. |

## Restrictions

`0` does not establish that the position was reset. Pipes, including [$exec()](exec.md) streams and pipe-backed decompression streams, cannot be rewound this way. For ordinary seekable streams, [$fseek()](fseek.md) with offset `0` and origin `set` exposes the seek result instead.

Archive-backed handles provided with libarchive support have no ordinary C file stream. The current implementation passes their null stream pointer to `fseek()`, which can crash EPIC. Do not call `$frewind()` on those handles.

## Examples

For a known ordinary, seekable file handle:

```irc
@ first_line = read($handle)
@ rewind_result = frewind($handle)
@ first_line_again = read($handle)
```

The second read repeats the first line only if the seek actually succeeds; `rewind_result == 0` alone cannot confirm success.

## Source

Registration and argument parsing: [source/functions.c](../../source/functions.c). Handle lookup and unchecked seek: [source/files.c](../../source/files.c). Ordinary and archive-backed stream construction: [source/elf.c](../../source/elf.c). The [POSIX seek specification](https://pubs.opengroup.org/onlinepubs/7908799/xsh/fseek.html) describes successful repositioning and EOF clearing.
