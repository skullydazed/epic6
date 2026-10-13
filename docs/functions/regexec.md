# regexec function

## Synopsis

`$regexec(<compiled expression> [text])`

## Technical

Attempts to match text against a successfully compiled expression returned by [$regcomp()](regcomp.md) or [$regcomp_cs()](regcomp_cs.md). The first argument is one normal function word containing the unchanged Base85-encoded compiled state. All remaining text is passed to the regex engine as one string, without further word splitting or removal of literal quotes. Omitting that text tests an empty string.

Matching uses the flags selected at compilation, including case sensitivity. The wrapper supplies no execution flags and does not request capture positions. Expressions can match a substring unless their pattern anchors require a whole-string match. Use [$regmatches()](regmatches.md) when capture offsets are needed.

## Returns

`0` when a match is found. A nonzero regex-engine code means no match or an execution error; the specific numeric codes depend on the engine. Missing compiled-expression input returns the empty string instead of an engine code.

The result updates the shared last-regex-error state. To obtain its message, pass the compiled string to [$regerror()](regerror.md), not the numeric return code, before another regex operation changes the shared state. A successful match sets that state to zero.

Test `regexec(...) == 0` for a match rather than treating the return value as a conventional true-on-match boolean. The wrapper produces no normal output and requires no IRC connection.

## Restrictions

Only use a successfully compiled string from the current process. Base85 decoding is unchecked, and there is no active encoded-length validation; fabricated, damaged, unsuccessfully compiled, or freed state can lead to invalid library access rather than a checked error return. The function does not release compiled resources. See [regcomp](regcomp.md) for lifetime and the current cleanup limitation.

The configured PCRE2 POSIX interface or platform regex library determines pattern syntax, match behavior, and error codes.

## Examples

After successful compilation with `@ pattern = regcomp(^hello$)`, `$regexec($pattern HELLO)` returns `0`, and `$regexec($pattern goodbye)` returns a nonzero no-match code. `$regerror($pattern)` immediately afterward describes that no-match result.

A successfully compiled unanchored `hello` pattern can match `say hello now`; `^hello$` requires the entire tested string to match.

## History

First appeared in epic4-1.029.

## Source

Registration, `function_regexec()`, and shared error state: [source/functions.c](../../source/functions.c). Base85 decoding: [source/ircaux.c](../../source/ircaux.c). Engine selection: [include/irc_std.h](../../include/irc_std.h) and [configure.ac](../../configure.ac).
