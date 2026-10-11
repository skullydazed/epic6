# ftell function

## Synopsis

`$ftell(<file handle>)`

## Technical

Returns the current position of an ordinary file stream, measured in bytes from the beginning. The handle must be an EPIC file handle from [$open()](open.md), rather than an operating system file descriptor. The first argument word is converted to an integer; additional words are ignored. Missing input returns the empty string.

Use the result to save a position and later restore it with `$fseek(<handle> <saved-position> set)`. The value is a byte offset, not a line number, Unicode character count, or display-column count. Querying it does not move the stream's position.

## Returns

A nonnegative byte position as an integer string, or `-1` if the handle is unregistered, has been closed, or the underlying `ftell()` call fails. Missing input returns the empty string. The function returns no diagnostic text or operating system error number.

## Restrictions

Pipes, including [$exec()](exec.md) streams and pipe-backed decompression streams, do not provide a seekable file position and normally fail this query.

With libarchive support, archive-backed handles have no ordinary C file stream. The current implementation passes their null stream pointer to `ftell()`, which can crash EPIC. Use this function only for ordinary streams.

The implementation uses C `ftell()`, whose result has type `long`, and then converts that result to EPIC's integer string. It does not use `ftello()`, so the conversion cannot recover positions beyond the platform's `long` range. See [$fsize()](fsize.md) for a pathname's size rather than a stream position.

## Examples

Save and restore a known ordinary, seekable stream position:

```irc
@ saved_position = ftell($handle)
if (saved_position >= 0) {
    @ line = read($handle)
    @ seek_result = fseek($handle $saved_position set)
}
```

If `seek_result` is `0`, the next read resumes from the saved position.

## Source

Registration and argument parsing: [source/functions.c](../../source/functions.c). Handle lookup and direct `ftell()` call: [source/files.c](../../source/files.c). Ordinary and archive-backed stream construction: [source/elf.c](../../source/elf.c).
