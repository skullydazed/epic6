# Stat

For local filesystem metadata, see the [stat function](../functions/stat.md):

```text
$stat(filename)
```

It returns thirteen words describing the resolved filesystem entry. The filename always supports double-quoted grouping for spaces. Missing input or path-normalization failure returns `-1`; a failed operating-system `stat()` call returns an empty string. Symbolic links are resolved, so the result describes the target.

| Indices | Information |
| --- | --- |
| `0`–`6` | Device, inode, file type/mode in octal, link count, owner UID, group GID, and special-device number. |
| `7`–`9` | Byte size, preferred I/O block size, and allocated block count. |
| `10`–`12` | Access, modification, and inode-change timestamps. |

Size and block fields use `uintmax_t` formatting rather than a fixed 32-bit truncation rule. Some identifier fields are converted to C `int`, so they can lose information on platforms with wider source fields. See the function page for exact representations and platform limits.

Other similarly named topics are [stats](../commands/stats.md), which requests server-defined IRC statistics, and [status lines](../special/status_line.md), which display client state. `stat()` itself sends no IRC request.

## Source references

- `source/functions.c`: `stat()` argument parsing, normalization, system call, and result formatting.
- `source/commands.c`: separate `stats` protocol command.
