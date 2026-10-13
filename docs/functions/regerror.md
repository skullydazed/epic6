# regerror function

## Synopsis

`$regerror(<compiled expression>)`

## Technical

Returns a message describing the most recent regex error code. Supply the unchanged compiled string returned by [$regcomp()](regcomp.md) or [$regcomp_cs()](regcomp_cs.md), normally the one used in the operation being checked.

The error code is shared globally between regex operations, not stored separately for each compiled expression. Compilation, [$regexec()](regexec.md), and [$regmatches()](regmatches.md) overwrite it, including setting it to zero on success. Calling `regerror` does not clear it. Check immediately after the operation of interest, before another regex operation changes the state.

Only the first normal function word is used; additional arguments are ignored. It is decoded from Base85 into a `regex_t` structure and passed to the configured engine's error-message routine when the shared code is nonzero. The wording depends on the regex engine and platform. A no-match result from `regexec` is a nonzero code and can produce a message even though it is an ordinary unsuccessful match rather than a compilation failure.

## Returns

The latest regex error message, or the empty string when the last code is zero. Missing input also returns empty. Messages are limited by the wrapper's 1024-byte buffer. The function produces no normal screen output and requires no IRC connection.

A nonempty result is suitable for an error condition such as `if (regerror($pattern))`; a successful compilation gives an empty, false result.

## Restrictions

An empty return is not proof that the supplied compiled string is valid: the wrapper checks the shared code, not the string's ownership or compilation status. Decoding failures are unchecked and the destination structure is not initialized before decoding. Supply only an original, unchanged compiled string from the current process, and never a fabricated, corrupted, or freed one.

## Examples

```text
@ pattern = regcomp(^hello$)
if (regerror($pattern)) {
    xecho -b Error compiling regex: $regerror($pattern)
} else {
    @ matched = regexec($pattern goodbye)
    xecho -b Match result: $matched, message: $regerror($pattern)
}
```

After successful compilation, the match fails because `goodbye` does not match `^hello$`; the final query describes that no-match code. Compiled resources and the current cleanup limitation are described in [regcomp](regcomp.md).

## History

First appeared in epic4-1.029.

## Source

Registration, `function_regerror()`, shared error state, and its writers: [source/functions.c](../../source/functions.c). Base85 decoding: [source/ircaux.c](../../source/ircaux.c). Regex engine selection: [include/irc_std.h](../../include/irc_std.h).
