# regcomp function

## Synopsis

`$regcomp(<regex pattern>)`

## Technical

Compiles a case-insensitive regular expression into a string suitable for the related regex functions. The entire argument string is the pattern, including spaces and literal quotes; no word splitting or quote removal occurs in this handler. Empty input is passed to the regex compiler rather than treated as a missing-argument error.

Compilation requests `REG_EXTENDED` and `REG_ICASE`, plus `REG_ENHANCED` when the library provides it. Builds with PCRE2 use its POSIX compatibility interface; other builds use the platform's regex library. Supported expression syntax and case handling therefore depend on the configured engine. [$regcomp_cs()](regcomp_cs.md) is the case-sensitive counterpart.

## Returns

An ASCII85/Base85-encoded representation of the compiled `regex_t` structure, with `<~` and `~>` delimiters. It contains process-local library state, including pointers; it is not a portable or persistent compiled pattern. Use the original string unchanged with [$regexec()](regexec.md), [$regmatches()](regmatches.md), and [$regerror()](regerror.md).

A string is produced even when compilation fails. Compilation updates a shared last-regex-error value; call `regerror` with the returned string before another regex operation overwrites that error. A nonempty result alone does not establish successful compilation. Do not execute an unsuccessfully compiled pattern.

## Restrictions

Compiled expressions allocate library resources and are intended to be released with [$regfree()](regfree.md). The current cleanup wrapper still requires a string length of twice `sizeof(regex_t)`, an obsolete encoding check that generally rejects these Base85 results with a diagnostic. Thus normal cleanup is impaired in this implementation; repeatedly compiling expressions can retain allocations.

Copies of a returned string refer to the same underlying resources, not independently owned compiled expressions. Do not modify, manufacture, reuse after freeing, or transfer these strings to another process. The consumer wrappers do not reliably validate decoded data. No IRC connection is required.

## Examples

`$regcomp(^hello$)` compiles a case-insensitive expression for the complete text `hello`. A successful compiled result can be used with `regexec` to test both `hello` and `HELLO`.

`$regcomp_cs(^hello$)` requests case-sensitive matching instead. Store the returned compiled string rather than recompiling it for every match, and inspect `regerror` immediately after compilation.

## History

First appeared in epic4-1.029.

## Source

Registration, `function_regcomp()`, error state, and related consumers: [source/functions.c](../../source/functions.c). Base85 encoding: [source/ircaux.c](../../source/ircaux.c). Engine selection: [include/irc_std.h](../../include/irc_std.h) and [configure.ac](../../configure.ac).
