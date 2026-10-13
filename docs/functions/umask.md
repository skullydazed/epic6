# umask

## Synopsis

`$umask(<mask>)`

## Technical

Sets EPIC's process-wide file creation mask and returns the previous mask. The first function word is parsed as an integer using base-zero conversion: use a leading zero for octal permission notation, for example `077`. Bare `77` is decimal, not octal. Additional words are ignored.

The mask specifies permission bits to remove from newly created files and directories. With ordinary Unix mode handling, the resulting mode is `requested_mode & ~mask`, not arithmetic subtraction. The system uses only the low nine permission bits (`0777`). Default ACLs can alter creation behavior; see the [system umask interface](https://man7.org/linux/man-pages/man2/umask.2.html).

## Practical

Use this function to control permissions for future file creation by EPIC and newly launched child processes. It does not change existing file permissions or affect [`chmod`](chmod.md)'s explicit permission changes. It is not limited to EPIC's built-in file functions.

For example, `077` normally reduces a requested directory mode of `0777` to `0700`, and a requested regular-file mode of `0666` to `0600`. It cannot add execute bits absent from the requested mode.

## Returns and restrictions

The previous mask as a decimal integer, not an octal string. For example, previous mask `077` is returned as `63`. Preserve that return value if you intend to restore the mask later.

Missing input returns empty without changing the mask; there is no query-only form. Nonnumeric input becomes zero and therefore clears the mask. Numeric prefixes are accepted, and parsing overflow or narrowing to C `int` is not reported. Use a valid permission mask. There are no options, settings, or server requirements.

## Examples

```irc
$umask(077)    Set the creation mask to octal 077; return the previous mask in decimal.
$umask(002)    Normally allow directory mode 0775 and file mode 0664 when requested as 0777/0666.
$umask()       Return empty without changing the mask.
```

These expressions change the process mask when evaluated; their returned value is not the resulting file mode.

## Source

Handler: [`source/functions.c`](../../source/functions.c:5305). Argument and return conversion: [`include/functions.h`](../../include/functions.h:25).
