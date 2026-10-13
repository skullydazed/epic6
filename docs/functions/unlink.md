# unlink

## Synopsis

`$unlink(<file> [<file> ...])`

## Technical

Removes the specified file pathnames from the filesystem. Each filename is always parsed as a quoted-word argument, so quote names containing spaces regardless of `xdebug dword` mode. Multiple filenames are processed in order; a failure does not stop later attempts.

Each pathname is normalized before removal: a leading `~` or `~user` is expanded, and `realpath` resolves relative paths, `.`/`..`, and symbolic links. The path must resolve to an existing target. Tildes elsewhere in a filename are literal; wildcards are not expanded.

## Practical

Use this function to remove files such as old client logs. It unlinks a pathname rather than securely erasing file contents. Use [`rmdir`](rmdir.md) for directories.

## Returns

A decimal failure count: `0` if every attempted removal succeeded, otherwise the number of normalization or unlink failures. Missing input also returns `0`, because no paths were attempted. The result does not identify which paths failed, and the handler prints no per-path error.

## Restrictions

Symbolic links are followed during normalization: supplying a symlink removes its resolved target pathname, leaving the symlink itself behind. A broken symlink fails normalization and is not removed. This differs from calling the operating system's `unlink` directly on the original symlink name.

Filesystem permissions and other operating-system restrictions can prevent removal. Directories are not recursively removed. An empty quoted filename triggers a special fallback: the entire remaining argument is treated as one final pathname, rather than continuing normal filename parsing. Avoid empty filename arguments.

There are no options, settings, or server requirements. The function performs removal when evaluated.

## Examples

```irc
$unlink(~/old-client.log)                    Remove that file; return 0 on success.
$unlink("old client.log" ~/another-old.log)   Attempt both paths and return the failure count.
$unlink()                                   Return 0 without attempting removal.
```

## Source

Handler: [`source/functions.c`](../../source/functions.c:4653). Path normalization: [`normalize_filename`](../../source/ircaux.c:1786). Tilde expansion: [`expand_twiddle`](../../source/ircaux.c:1819).
