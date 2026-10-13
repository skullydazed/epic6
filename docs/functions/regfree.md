# regfree function

## Synopsis

`$regfree(<compiled expression>)`

## Technical

Intended to release the library resources associated with an expression returned by [$regcomp()](regcomp.md) or [$regcomp_cs()](regcomp_cs.md). The first normal function word is taken as the compiled string; extra arguments are ignored.

The current implementation first requires that string's length to equal twice `sizeof(regex_t)`. This is an obsolete check for the earlier encoding: the compilation functions now produce Base85 strings, which generally fail it. Rejected input produces the diagnostic `First argument to $regmatches() isn't proper length` and no resources are freed. The diagnostic names `regmatches` even though this is `regfree`.

If the check passes, the wrapper decodes the string from Base85 and calls the regex library's `regfree()`. It neither checks decoding success nor initializes the destination structure before decoding. Do not pad or alter a compiled string to bypass the check; that can result in invalid library access rather than cleanup.

## Returns

Always the empty string, including missing input, rejected input, and the path that calls the library cleanup routine. The result therefore does not confirm that resources were released. Missing input produces no diagnostic. This operation does not change the shared error state queried by [$regerror()](regerror.md).

## Restrictions

When resources have actually been freed, do not free them again or use the compiled string with [$regexec()](regexec.md), [$regmatches()](regmatches.md), or `regerror`. Copies of the string share the same underlying resources and all become unusable together. Freeing does not erase the EPIC variable containing the string.

Normal cleanup of current compiled results is impaired by the length check, so repeated compilations can retain allocations. Use only an original compiled string from the current process; arbitrary, corrupted, or unsuccessfully compiled state is not a supported cleanup input. No IRC connection is required.

## Example

`$regfree($pattern)` attempts to release the expression stored in `pattern`, returning empty. With a normal current Base85 result, expect the obsolete length-check diagnostic rather than assume that this freed the expression.

## History

First appeared in epic4-1.029.

## Source

Registration, `function_regfree()`, and compilation encoding: [source/functions.c](../../source/functions.c). Base85 decoding: [source/ircaux.c](../../source/ircaux.c). Regex library selection: [include/irc_std.h](../../include/irc_std.h).
