# ftruncate function

## Synopsis

`$ftruncate(<bytes> <filename>)`

## Technical

Changes the named regular file to exactly `bytes` bytes in size, using the operating system's pathname-based `truncate()` call. This takes a filename, not an EPIC file handle, despite the function's name.

Shrinking discards all data beyond the new length. Growing a regular file extends it with zero-filled space when the filesystem supports the operation. A length of zero clears its contents. The file must already exist and permit the operation; missing files are not created. Symbolic links resolve to their targets.

The first argument is converted with `strtoimax()` using base detection, accepting decimal, hexadecimal, and leading-zero octal notation. Nonnumeric input converts to zero, and numeric prefixes with trailing nonnumeric text can be accepted. Supply an intentional nonnegative length: an accidentally nonnumeric length can clear a file, while negative lengths are rejected by the operating system.

The remaining argument text is the literal pathname, including spaces and quotation marks. Do not surround a filename containing spaces with quotes merely to group it. There is no tilde expansion, filename normalization, script-path search, or archive-member interpretation. Relative paths use EPIC's current working directory, which [/cd](../commands/cd.md) can change.

## Practical

This can clear a logfile that has grown too large, or resize another regular file. It does not coordinate with EPIC's open stream handles, flush their buffers, or reset their positions. A later buffered or positioned write can therefore restore data or extend the file again. Coordinate active writers before relying on the new size.

## Returns

The empty string on successful truncation, or an operating system error message suitable for [/echo](../commands/echo.md) on failure. Missing the byte-length argument also returns the empty string without performing the operation, so that result alone does not validate a missing call. A supplied length without a filename attempts an empty pathname and returns its error message.

## Examples

For an existing file you intend to clear:

```irc
@ resize_error = ftruncate(0 /path/to/application.log)
if (strlen($resize_error)) {echo $resize_error}
```

`$ftruncate(1024 /path/to/my file.log)` resizes the literal path containing a space to 1,024 bytes. Any bytes removed are lost; these examples modify the named files when executed.

## Source

Registration, length conversion, direct `truncate()` call, and error-message return: [source/functions.c](../../source/functions.c). Integer argument conversion: [include/functions.h](../../include/functions.h). [POSIX truncation semantics](https://pubs.opengroup.org/onlinepubs/9699919799/functions/ftruncate.html) describe regular-file resizing.
