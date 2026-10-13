# regcomp_cs function

## Synopsis

`$regcomp_cs(<regex pattern>)`

## Technical

Compiles a case-sensitive regular expression into a string suitable for the related regex functions. It requests `REG_EXTENDED`, plus `REG_ENHANCED` when provided by the regex library, without `REG_ICASE`. [$regcomp()](regcomp.md) requests case-insensitive compilation instead.

The entire argument string is the pattern, including spaces and literal quotes. No word splitting or quote removal occurs in the handler, and empty input is passed to the regex compiler. Builds configured with PCRE2 use its POSIX compatibility interface; otherwise the platform regex library supplies the syntax and behavior.

## Returns

An ASCII85/Base85-encoded representation of the compiled `regex_t` structure, with `<~` and `~>` delimiters. Use the unchanged string with [$regexec()](regexec.md), [$regmatches()](regmatches.md), and [$regerror()](regerror.md).

The structure is encoded even if compilation fails. Check `regerror` immediately after compilation, before another regex operation overwrites the shared last-error value. A nonempty compiled string alone is not evidence of success. Do not execute a pattern whose compilation failed.

## Example

```text
@ orig_string = [ABC DEF]
@ pattern = regcomp_cs(abc)
if (regerror($pattern)) {
    xecho -b Error compiling regex: $regerror($pattern)
} else {
    if (regexec($pattern $orig_string) == 0) {
        xecho -b It matched!
    } else {
        xecho -b Regex error: $regerror($pattern)
    }
}
```

With successful compilation, `abc` does not match `ABC DEF` because matching is case-sensitive. The second error query reports the engine's no-match message.

## Restrictions

The encoded structure includes process-local library state and pointers; it cannot be persisted for another run or transferred to another process. Copies share the same underlying compiled resources. Do not modify, manufacture, or reuse a freed compiled string; consumers do not reliably validate decoded input.

Compiled resources are intended to be released with [$regfree()](regfree.md). The current wrapper's obsolete requirement that the encoded length equal twice `sizeof(regex_t)` generally rejects Base85 results, so normal cleanup is impaired and repeated compilations can retain allocations. No IRC connection is required.

## History

First appeared in epic4-1.029.

## Source

Registration, `function_regcomp_cs()`, error state, and related consumers: [source/functions.c](../../source/functions.c). Base85 encoding: [source/ircaux.c](../../source/ircaux.c). Engine selection: [include/irc_std.h](../../include/irc_std.h) and [configure.ac](../../configure.ac).
