# $isfilevalid()

## Synopsis

`$isfilevalid(<file handle>)`

## Description

Returns true if the handle is present in EPIC's open-file registry, and false otherwise. This includes handles returned by [open](open.md), [connect](connect.md), or [exec](exec.md). Once [close](close.md) removes a handle, it is no longer valid.

These are EPIC file handles, not operating-system file descriptors, server references, or filenames. A raw descriptor number is not a substitute; if it happens to equal a registered EPIC handle, the function tests that EPIC handle.

The first argument is converted to an integer; additional arguments are ignored. Conversion accepts decimal, hexadecimal, and leading-zero octal. Nonnumeric text converts to zero, which is not an issued file handle. Missing input returns the empty string.

Validity only means that EPIC recognizes the handle. It does not check read/write mode, EOF, stream errors, peer availability, or whether the next [read](read.md) or [write](write.md) operation will succeed. Archive-backed input handles also qualify; this does not make operations requiring an ordinary file stream available on them.

## Returns

| Result | Meaning |
| --- | --- |
| `1` | The handle exists in EPIC's open-file registry. |
| `0` | The handle is not registered, including a closed handle. |
| Empty string | The required argument is missing. |

## Examples

```text
$isfilevalid($file_handle)
$isfilevalid(-1)
```

The first checks a previously stored handle. The second returns `0`, as `-1` is not an issued handle. The function performs no I/O and does not close or change the stream.

## History

This function first appeared in EPIC4-1.1.6.

## Source

Registered and implemented as `function_isfilevalid` in [source/functions.c](../../source/functions.c). Handle allocation, `file_valid`, lookup, and removal are in [source/files.c](../../source/files.c); numeric conversion is in [source/ircaux.c](../../source/ircaux.c).
