# $close()

## Synopsis

`$close(<file handle>)`

## Technical

Closes the file associated with a handle returned by [`$open()`](open.md) and removes the handle from EPIC's open-file list. These handles are EPIC identifiers, not operating-system file descriptors. A closed handle cannot be used again.

Only the first argument is used. It is converted to an integer using EPIC's usual numeric conversion; additional arguments are ignored. Calling `$close()` without an argument returns an empty string.

## Practical

Use this function when finished reading or writing a file opened with `$open()`. Closing an ordinary output file attempts to flush buffered data through the underlying `fclose()` call. The return value does not confirm that flushing succeeded or that data is durably stored on disk.

Handles returned by [`$exec()`](exec.md) also belong to this file-handle list and can be closed individually. Closing such a handle closes its stream; this function does not wait for the child process or return its exit status.

## Returns

| Value | Meaning |
| --- | --- |
| `0` | A registered handle was removed and its underlying close operation was attempted. |
| `-1` | The handle was not registered, including one already closed or a failed `$open()` result. |
| Empty string | No argument was supplied. |

Errors from the underlying close operation are discarded, so `0` is not a guarantee of successful output delivery.

## Examples

```irc
@ handle = open(notes.txt r)
if (handle > 0) {
    echo $read($handle)
    @ result = close($handle)
}
```

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). Handle lookup and removal are in [source/files.c](../../source/files.c); underlying stream and archive closing is in [source/elf.c](../../source/elf.c).
