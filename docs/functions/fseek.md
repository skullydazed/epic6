# fseek function

## Synopsis

`$fseek(<file handle> <position> <anchor>)`

## Technical

Sets an ordinary file stream's position to `position` bytes away from `anchor`. After a successful seek, [$read()](read.md) or [$readb()](readb.md) starts reading at the new position. Use an EPIC file handle returned by [$open()](open.md), rather than an operating system file descriptor.

Position can be positive or negative, provided the resulting position is valid. All three arguments are required; additional words are ignored. The handle and position are converted with `strtoimax()` using base detection, accepting decimal, hexadecimal, and leading-zero octal notation. Nonnumeric input converts to zero, and a numeric prefix can be accepted despite trailing nonnumeric text.

Anchor names are case-insensitive and must match completely:

| Anchor | Offset is relative to |
| --- | --- |
| `set` | The beginning of the file. |
| `cur` | The current position. |
| `end` | The end of the file. |

A successful seek clears the end-of-file indicator. It does not explicitly clear the stream's error indicator. Positions are byte offsets, rather than line, Unicode character, or display-column positions; an offset inside a multibyte character does not identify a complete character.

## Returns

| Result | Meaning |
| --- | --- |
| `0` | The seek succeeded. |
| `-1` | Unregistered handle, unsupported anchor, or an operating system seek failure. |
| Empty string | A required argument was missing. |

The underlying seek result is returned directly. The POSIX `fseeko()` used here reports failure as `-1`, not `1`. No error message or operating system error number is returned by this function.

## Restrictions

Pipes, including [$exec()](exec.md) streams and pipe-backed decompression streams, are not seekable. Archive-backed handles provided with libarchive support have no ordinary C file stream; with a recognized anchor, the implementation passes their null stream pointer to `fseeko()`, which can crash EPIC. Use this function only for ordinary streams.

Seeking beyond the end of a regular file does not itself extend it. A later write may extend it; append-mode writes still go to the end regardless of the seek position. See [$ftell()](ftell.md) to inspect the current position and [$frewind()](frewind.md) for a reset that discards seek errors.

## Examples

For a known ordinary, seekable handle:

```irc
@ seek_result = fseek($handle 0 set)
if (seek_result == 0) {echo $read($handle)}
```

`$fseek($handle 10 cur)` moves ten bytes forward; `$fseek($handle -10 end)` moves to ten bytes before the end if that position is valid.

## Source

Registration and argument parsing: [source/functions.c](../../source/functions.c). Handle lookup and anchor dispatch: [source/files.c](../../source/files.c). Stream construction: [source/elf.c](../../source/elf.c). [POSIX seek semantics](https://pubs.opengroup.org/onlinepubs/7908799/xsh/fseek.html) define repositioning and EOF clearing; [append-mode semantics](https://pubs.opengroup.org/onlinepubs/7908799/xsh/fopen.html) define where append writes occur.
