# read function

## Synopsis

`$read(<file handle> [byte count])`

## Technical

Reads from an EPIC file handle previously returned by [$open()](open.md), or a readable handle returned by [$exec()](exec.md). This is an EPIC handle, not an operating-system file descriptor. The first argument selects the handle; an optional second argument selects binary reading. Extra arguments are ignored.

Without a byte count, reads through the next newline or end of file, extending the buffer as needed rather than imposing a fixed line-length limit. The newline is removed; a preceding carriage return is retained. A final line without a newline can still be returned. Text that is not valid UTF-8 is passed through the configured `scripts` recoding rule; already valid UTF-8 is returned without that conversion. See [/encoding](../commands/encoding.md).

With a byte count, reads up to that many bytes and normally returns them encoded using the CTCP transform. This includes embedded zero bytes without treating them as text terminators. It performs no text recoding or newline removal in binary mode. The read can return fewer bytes at EOF. [$xform()](xform.md) can decode or convert the returned data.

## Returns

Line text, or CTCP-encoded binary data. Missing input, an invalid handle, no data at EOF, and ordinary stream read errors can return the empty string. A blank line or zero-byte binary request also returns empty, so the result alone does not identify the cause. Use [$isfilevalid()](isfilevalid.md), [$eof()](eof.md), and [$ferror()](ferror.md) to inspect handle and stream state.

Ordinary file reads clear the stream's previous EOF and error indicators before attempting the read; check those indicators after the operation. A successfully returned final unterminated line may already have EOF set. The handle stays open and its position advances by the consumed data.

## Restrictions

Supply a nonnegative, reasonably sized byte count. Negative values are converted to an unsigned allocation size, and oversized requests are not range-checked; they can exhaust memory or overflow size arithmetic. Text mode is unsuitable for binary data containing zero bytes.

Archive-member handles use the archive reader when built with libarchive. Stream state for such handles follows that reader rather than ordinary stdio indicators. If CTCP transformation fails, the binary helper falls back to the raw buffer, so encoding is not guaranteed on that failure path.

Reads are synchronous and can block while waiting for a pipe or other stream. No IRC connection is required by the function itself.

## Examples

| Expression | Result |
| --- | --- |
| `$read(4)` | The next line from valid handle 4, without its newline. |
| `$read(4 30)` | Up to the next 30 bytes from handle 4, normally CTCP-encoded. |
| `$read(4 0)` | Empty string without consuming data. |

## Source

Registration and `function_read()`: [source/functions.c](../../source/functions.c). `file_read()`, `file_readb()`, handle lookup, and recoding: [source/files.c](../../source/files.c). Ordinary and archive stream wrappers: [source/elf.c](../../source/elf.c).
