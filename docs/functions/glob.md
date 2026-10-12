# glob function

## Synopsis

`$glob(<file glob pattern> [<pattern> ...])`

## Technical

Finds file and directory names matching filesystem glob patterns. Each pattern is a double-quote-aware word regardless of `/xdebug dword`; quote a pattern containing spaces. Multiple patterns are processed in the supplied order. Their individually sorted match lists are concatenated without removing duplicates.

Matching uses the system's pathname globbing rules, including `*`, `?`, and bracket expressions. It is separate from EPIC's IRC wildcard matcher. Hidden files require an explicit leading dot in the corresponding pattern component. Relative patterns use EPIC's current working directory. `~` and `~/` expand to EPIC's home directory; `~user/` uses the named user's home directory.

The system glob call uses only `GLOB_MARK`: matching directories have a trailing `/`, and results are sorted using the platform's collation rules. It does not enable brace expansion, recursive `**` traversal, or return an unmatched pattern as a filename. This is pathname matching rather than executing a shell; shell variables and command substitutions are not evaluated by globbing. See the [POSIX glob interface](https://pubs.opengroup.org/onlinepubs/9799919799/functions/glob.html) for the underlying matching and sorting rules.

Nonempty pattern words are CTCP-decoded before matching. Backslashes can therefore be consumed or interpreted before the system's own glob escaping; they are not passed through unchanged.

## Practical

Use this function to list files or find names with similar spellings. It does not open the returned files or guarantee that a later operation on them will succeed.

## Returns

A space-separated list of matching pathnames. Paths containing a space or double quote are surrounded with double quotes. The result is empty when no matches are accumulated or input is missing.

Error handling depends on the platform: negative system glob error codes are returned as integer strings, except a negative no-match code returns empty. Either case ends the entire call and discards earlier patterns' results. On platforms with positive glob error codes, this wrapper does not recognize those codes as errors; do not rely on its result to distinguish an error from no matches.

## Examples

| Expression | Matches |
| --- | --- |
| `$glob(~/*)` | Non-hidden entries in EPIC's home directory. |
| `$glob(*)` | Non-hidden entries in the current directory. |
| `$glob(.*)` | Dot entries in the current directory; inclusion of `.` and `..` depends on the system glob implementation. |
| `$glob(f*)` | Current-directory names beginning with `f`. |
| `$glob("my folder/*.txt")` | Matching names inside the relative directory `my folder`. |

## Restrictions

This built-in is registered unconditionally in the current source and relies on the Unix `glob()` interface; it has no optional configure switch.

Returned names are not fully escaped: embedded double quotes are wrapped but not escaped, and tabs or newlines do not trigger quoting. Such filenames may not survive subsequent word-list parsing correctly.

An unknown `~user` makes tilde expansion fail, but this wrapper ignores that failure and may use uninitialized or previously retained pathname data. Use a known user or an explicit path; do not rely on a safe no-match result for an unknown user.

## Source

Registration, pattern parsing, `GLOB_MARK`, return quoting, and error handling: [source/functions.c](../../source/functions.c). Tilde expansion and CTCP decoding: [source/ircaux.c](../../source/ircaux.c). Double-quote-aware parsing: [include/functions.h](../../include/functions.h) and [include/words.h](../../include/words.h). System glob declaration: [include/irc_std.h](../../include/irc_std.h).
