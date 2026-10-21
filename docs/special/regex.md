# Regular expressions

## Synopsis

```text
$regcomp(pattern)
$regcomp_cs(pattern)
$regexec(compiled-pattern text)
$regmatches(compiled-pattern count text)
$regerror(compiled-pattern)
$regfree(compiled-pattern)
```

These C built-in functions compile and execute regular expressions. They are separate from EPIC's [wildcard patterns](patterns.md). Their syntax is supplied by the regex library selected at build time: the PCRE2 POSIX interface when configured, otherwise the platform's POSIX regex interface. Compilation requests extended syntax, plus the platform's enhanced flag when available; do not assume every build accepts the same extensions.

## Compiling and matching

[Regcomp](../functions/regcomp.md) compiles a case-insensitive pattern; [regcomp_cs](../functions/regcomp_cs.md) omits the case-insensitive flag. Each returns an opaque string suitable for storing in a script variable and passing unchanged to the other functions. The string encodes a library structure with process-local resources; it is not a portable serialized regex, ordinary lvalue name, or pattern that can be saved and restored in a later client session.

Compilation can fail even though a nonempty opaque string is returned. Check [regerror](../functions/regerror.md) immediately after compilation, before attempting to use the handle for matching. Do not pass unsuccessfully compiled or invented handles to the execution functions.

[Regexec](../functions/regexec.md) applies a successfully compiled expression to the supplied text. It returns the regex library's numeric status: `0` means a match; nonzero means no match or another library error. It is therefore incorrect to use a true result as “matched,” or to assume every nonzero result is exactly `1`.

Regular expressions can match a substring unless the pattern explicitly anchors its beginning and end. For example, `abc` can match inside `abc def`; use `^abc$` when the whole text must be exactly that value, subject to the selected regex syntax and flags.

## Errors are shared state

`regerror(handle)` returns a human-readable error string, or empty when the shared status is zero. The status records the most recent `regcomp`, `regcomp_cs`, `regexec`, or successful entry into `regmatches` library execution; it is not stored independently with each handle. An operation on another expression can overwrite the status before you inspect it.

A no-match status can also have an explanatory string, so a nonempty error description does not by itself distinguish invalid syntax from ordinary nonmatching input. Check the operation's result in context. `regfree` does not reset this shared status.

## Substring matches

[Regmatches](../functions/regmatches.md) is intended to return start/length pairs for up to `count` match slots. Slot zero describes the whole match; subsequent slots describe parenthesized subexpressions. Positions are converted from library byte offsets to Unicode code-point offsets, suitable for [mid](../functions/mid.md), rather than returned as raw UTF-8 byte offsets. Output stops at the first unmatched slot; it is not guaranteed to contain every requested slot.

A nonmatch returns empty. However, the current wrapper also retains an obsolete encoded-length check requiring the input string to have length `2 * sizeof(regex_t)`. Compilation now produces Base85, whose length generally differs. Consequently, current compiled results are generally rejected before matching, with `First argument to $regmatches() isn't proper length`. That rejection does not update the shared library status. See the function page before relying on capture extraction.

## Resource lifetime and current cleanup limitation

Compiled expressions allocate library resources intended to be released with [regfree](../functions/regfree.md). Copies of an opaque string share the underlying resources; copying the text does not clone the compiled expression. Once actual cleanup has occurred, using any copy or freeing it again is invalid and can crash the client.

The current `regfree` wrapper has the same obsolete length check as `regmatches`. Normal Base85 handles generally fail it, so calling `regfree` does not reliably release current compiled results. Rejection even prints the diagnostic naming `$regmatches()`. `regfree` always returns the empty string, which is not confirmation that cleanup succeeded.

Do not pad, decode, or modify a handle to bypass these checks. The wrappers do not reliably validate decoded structures before library access. Repeated compilations can retain resources because cleanup is impaired; clearing the script variable alone does not free the regex library's allocation.

## Example

The following demonstrates compilation and matching, with immediate error inspection:

```irc
@ original = [abc def];
@ compiled = regcomp(abc);
if (regerror($compiled) != []) {
    xecho -b Compile error: $regerror($compiled);
} else {
    @ result = regexec($compiled $original);
    if (result == 0) {
        xecho -b It matched!;
    } else {
        xecho -b Regex status $result: $regerror($compiled);
    };
    @ regfree($compiled);
};
```

On successful compilation and execution it prints `It matched!`. The cleanup call is included as the intended lifecycle, but the current length-check defect can then emit a diagnostic and retain the allocation. This is not an example of guaranteed leak-free repeated use.

## Practical limits

Compile once and reuse a valid handle rather than recompiling for every event. The library, pattern, and input determine matching cost; regular expressions are not universally faster than wildcard matching. Keep text and patterns within the applicable library and client limits, and read the individual function pages for missing-input behavior and capture-count restrictions.

No IRC connection or script package is required. The regex backend is a build/platform dependency, and callback code using these functions still follows normal [expansion](expando_substitution.md) and [expression](expressions.md) rules.

## Source references

- `source/functions.c`: compilation flags, Base85 structures, shared `last_regex_error`, execution, capture conversion, and cleanup checks.
- `include/irc_std.h` and `configure.ac`: PCRE2 POSIX versus platform regex selection.
- `source/ircaux.c`: Base85 transform implementation.
- `include/functions.h`: function-argument parsing and return conversion.
