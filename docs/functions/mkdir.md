# $mkdir()

## Synopsis

`$mkdir(<directory> [<directory> ...])`

## Technical

Attempts to create one or more directories using the system `mkdir()` call with requested permissions `0777`, restricted by the process's current umask. There is no mode argument or recursive parent-directory option. Each path is a double-quoted word: surrounding quotes are removed and a path containing spaces can be quoted, independently of `/xdebug dword`.

Paths pass through EPIC's filename normalizer, which supports leading `~` and `~user` expansion and uses `realpath()` to canonicalize the result. **The current implementation has a defect:** this normalization requires an existing path, while directory creation needs a new path. When normalization fails, the function increments its failure count but still calls `mkdir()` using the normalization output buffer, whose contents are not guaranteed on failure. This can cause an additional failure or an attempt on an unintended path.

A path that already names a directory normally normalizes successfully but then fails `mkdir()` because it exists. The function does not treat an existing directory as success. It attempts subsequent supplied paths after failures; earlier effects are not rolled back. Do not rely on this implementation to create new directories correctly.

## Practical

The interface is intended for scripts that install directories. Because of the normalization defect, it cannot currently provide dependable directory creation. See [$umask()](umask.md) for the process permission mask and [$chmod()](chmod.md) for changing permissions separately.

## Returns

A nonnegative count of failure events: one for each failed normalization and one for each failed `mkdir()` call. Thus a single supplied path can contribute two failures, and the result is not necessarily the number of directories that could not be created. It does not identify the affected paths or return their error messages.

No arguments returns `0`, because no paths are attempted. A zero return should not be interpreted as proof that a requested new directory was created despite the defect described above.

## Examples

| Expression | Result or effect |
| --- | --- |
| `$mkdir()` | `0`; no filesystem operation |
| `$mkdir(~/myscript)` | Attempts one path; reliable creation cannot be assumed |
| `$mkdir("my script" another)` | Attempts two paths; the quoted path is one argument |

Implementation and registration: [source/functions.c](../../source/functions.c), `function_mkdir()`. Filename normalization and tilde expansion: [source/ircaux.c](../../source/ircaux.c), `normalize_filename()` and `expand_twiddle()`.
