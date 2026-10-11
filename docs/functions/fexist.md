# fexist function

## Synopsis

`$fexist(<file>)`

## Technical

Checks whether a path resolves to an existing object that passes the operating system's read-access check. The filename is one extended word: surround it with double quotes if it contains spaces. Additional arguments are ignored. Double-quoted words are recognized independently of the function word settings.

Tilde expansion supports `~/file` in your home directory and `~user/file` in another user's home directory. Relative paths use EPIC's current working directory, which [/cd](../commands/cd.md) can change. The path is canonicalized with `realpath()`, resolving symbolic links and `.` or `..` components, before testing read access with `access(..., R_OK)`.

A symbolic link succeeds only if its target resolves and passes that check; a dangling link fails. The object need not be a regular file: a readable directory can also return `1`. Unresolvable paths, unreadable objects, missing arguments, and an empty filename return `-1`. An empty quoted first argument does not select the remaining text as a literal filename.

## Practical

Use this function to check whether a file is present and readable before trying to load it. It does not return `0`, so compare its result with `1`; a generic nonzero test would also accept failure.

The test does not open the file or guarantee that a later open or load will succeed. Permissions or the path can change between the check and the operation. It checks filesystem paths, rather than searching EPIC's script load path.

## Returns

| Result | Meaning |
| --- | --- |
| `1` | The path resolves and passes the read-access check. |
| `-1` | Missing or empty filename, path normalization failure, or failed read-access check. |

## Examples

| Expression | Result |
| --- | --- |
| `$fexist(/etc/passwd)` | `1` if the file exists and passes the read-access check. |
| `$fexist(~/.ircrc)` | `1` if your startup file exists and passes the check. |
| `$fexist("my file.txt")` | Checks one filename containing a space. |
| `$fexist(fake_file)` | `-1` if that path does not exist. |
| `$fexist()` | `-1`. |

## History

First appeared in EPIC3pre7.

## Source

Registration and the read-access test: [source/functions.c](../../source/functions.c). Filename normalization, tilde expansion, and argument parsing: [source/ircaux.c](../../source/ircaux.c); the quoted-word macro is in [include/words.h](../../include/words.h).
