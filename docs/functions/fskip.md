# fskip function

## Synopsis

`$fskip(<file handle> [<number>])`

## Technical

Reads and discards up to `number` lines from the stream's current position. The default count is `1`. Use an EPIC file handle returned by [$open()](open.md) or a readable pipe handle from [$exec()](exec.md), rather than an operating system file descriptor.

The handle and count are converted with `strtoimax()` using base detection, accepting decimal, hexadecimal, and leading-zero octal notation. Nonnumeric input converts to zero; a numeric prefix can be accepted with trailing nonnumeric text. Extra arguments are ignored. Missing input returns the empty string.

The function checks [$eof()](eof.md) before each attempt, calls the same line-reading helper as [$read()](read.md), and discards the text. This is sequential reading, rather than seeking, so skipping a long line still requires reading it. A zero or negative count performs no reads.

## Returns and restrictions

Returns the number of read attempts, reduced by one if the stream's EOF indicator is set afterward. For ordinary newline-terminated input, this usually counts the lines discarded, but it is not a reliable count in every case:

- A handle already at EOF returns `-1`, even if the requested count is zero.
- An unregistered or closed handle also returns `-1`.
- A final line without a newline can be consumed by a read that sets EOF, causing that line not to be counted.
- Read errors are not checked by the skipping loop. Reads clear ordinary stream indicators before each attempt, so a reported count does not establish that every attempt consumed a line.

For a valid ordinary stream not at EOF, zero or negative counts return `0`. The function does not print the discarded lines.

Pipe reads can block until a complete line or EOF arrives, preventing EPIC from servicing its normal event loop. With libarchive support, archive-backed line reads do not reliably update their separate EOF flag; `$fskip()` may continue counting attempts after an archive entry is exhausted. Prefer ordinary streams when the count matters.

## Examples

`$fskip(4 3)` attempts to discard three lines from registered handle `4`. `$fskip(4)` attempts to discard one line.

For a known ordinary stream with sufficient newline-terminated input remaining:

```irc
@ skipped = fskip($handle 3)
echo Skipping returned $skipped
```

The next `$read($handle)` starts after the discarded input. Do not assume that `skipped` always equals the number of complete or partial lines consumed.

## Source

Registration and count parsing: [source/functions.c](../../source/functions.c). The skip loop, EOF test, and line-reading helper: [source/files.c](../../source/files.c). Ordinary and archive-backed reads: [source/elf.c](../../source/elf.c).
