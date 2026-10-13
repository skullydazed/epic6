# $stat()

## Synopsis

`$stat(<filename>)`

## Technical

Returns a thirteen-word description of a filesystem entry using the operating system's `stat()` call. The filename is a double-quoted word, so double quotes group paths containing spaces regardless of `/xdebug dword`. Only the first filename is used; additional arguments are ignored.

The path is normalized first: tilde prefixes are expanded, relative paths use the client's working directory, and `realpath()` resolves symbolic links and relative components. Consequently the result describes a link's target, not the symbolic link itself. The target must exist and be resolvable. Directories and other filesystem objects are accepted as well as regular files.

| Word index | Field | Representation |
| --- | --- | --- |
| `0` | Device containing the entry (`st_dev`) | Signed decimal after conversion to C `int` |
| `1` | Inode (`st_ino`) | Signed decimal after conversion to C `int` |
| `2` | File type and mode (`st_mode`) | Octal, without a prefix; includes type and special-mode bits as well as permissions |
| `3` | Hard-link count (`st_nlink`) | Signed decimal after conversion to C `int` |
| `4` | Owner UID (`st_uid`) | Signed decimal after conversion to C `int` |
| `5` | Group GID (`st_gid`) | Signed decimal after conversion to C `int` |
| `6` | Device represented by a special file (`st_rdev`) | Signed decimal after conversion to C `int` |
| `7` | Size in bytes (`st_size`) | Unsigned decimal `uintmax_t` |
| `8` | Preferred I/O block size (`st_blksize`) | Unsigned decimal `uintmax_t` |
| `9` | Allocated block count (`st_blocks`) | Unsigned decimal `uintmax_t` |
| `10` | Last data-access time (`st_atime`) | Signed decimal epoch seconds |
| `11` | Last data-modification time (`st_mtime`) | Signed decimal epoch seconds |
| `12` | Last inode/status-change time (`st_ctime`) | Signed decimal epoch seconds |

Timestamp fractions are omitted; `ctime` is not creation time. Block-count units and nonregular-file sizes follow the platform's filesystem interface; on Linux allocated blocks use 512-byte units. See the [file-status structure reference](https://www.man7.org/linux/man-pages/man3/stat.3type.html).

## Practical

Use this for several metadata fields in one call. [$fsize()](fsize.md) and [$ftime()](ftime.md) provide narrower queries. Retrieve a field with [$word()](word.md), after checking that a full result was returned.

## Returns

The thirteen space-separated fields on success. Missing or empty filename, or path-normalization failure, returns `-1`. A subsequent `stat()` failure returns empty instead. Thus most nonexistent-path failures return `-1`, not empty. The wrapper supplies no separate errno value or diagnostic.

Size and block fields are not explicitly truncated to 32 bits. Several identification fields are narrowed to C `int` and may lose information on platforms with wider native types. This function does not open or read file contents, and needs no IRC connection. There are no options.

## Examples

`$stat("/tmp/example file")` returns metadata for that existing path, or `-1` if it cannot be resolved.

`$stat()` returns `-1`. If `/tmp/missing-example` does not exist, `$stat(/tmp/missing-example)` also returns `-1`.

Registration and `function_stat()`: [source/functions.c](../../source/functions.c). Path normalization and quoted parsing: [source/ircaux.c](../../source/ircaux.c). Platform `Stat` type: [include/irc_std.h](../../include/irc_std.h).
