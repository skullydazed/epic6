# regmatches function

## Synopsis

`$regmatches(<compiled expression> <maximum pairs> [text])`

## Technical

Intended to match text like [$regexec()](regexec.md), while returning positions for the whole match and parenthesized subexpressions. The first pair describes the whole matched substring; subsequent pairs describe capture groups. Request at least two pairs to include the first capture. This reports captures from one match, not successive occurrences throughout the text.

The current wrapper requires the compiled string's length to equal twice `sizeof(regex_t)`. This obsolete encoding check generally rejects the Base85 strings now returned by [$regcomp()](regcomp.md) and [$regcomp_cs()](regcomp_cs.md). Rejection prints `First argument to $regmatches() isn't proper length` and returns empty without matching or changing the shared regex error state. Do not pad or alter the string to bypass that check.

If the check passes, the first argument is Base85-decoded and matching proceeds with the compilation flags and no additional execution flags. The maximum is parsed as an integer; all remaining text is tested as one string without further word splitting or removal of literal quotes. Missing text tests an empty string.

## Returns

On the matching path, returns at most the requested number of space-separated `start length` pairs. Starts are zero-based Unicode code-point positions and lengths are code-point counts, converted from the engine's byte offsets for use with [$mid()](mid.md). They are not display columns or grapheme counts. Valid UTF-8 text and matches at code-point boundaries are needed for meaningful conversion.

Output stops at the first capture with no match; later captures are omitted even if they matched. A zero-length capture has a length of zero. No match or an execution error returns empty and sets the shared code queried by [$regerror()](regerror.md); successful matching clears that code. With a maximum of zero, even success returns empty because no pairs are requested. Missing compiled input or maximum also returns empty without execution.

## Restrictions

Use only an original, successfully compiled string from the current process. Base85 decode results are unchecked and the destination structure is not initialized before decoding. Compiled lifetime and cleanup limitations are described in [regcomp](regcomp.md).

Supply a nonnegative, reasonably small maximum. Negative values become an unsigned count; oversized counts and allocation-size arithmetic are not range-checked and can exhaust memory or overflow. This operation requires no IRC connection.

## Example

For the successfully compiled extended expression `abc (def)` and text `abc def`, the intended result with a maximum of two is `0 7 4 3`: the whole match, then the `def` capture. `$mid(4 3 abc def)` extracts `def`. In the current implementation, the obsolete compiled-length check normally prevents obtaining those pairs from a freshly compiled Base85 string.

## History

First appeared in EPIC4-1.1.3; earlier compilation interfaces did not support subexpressions.

## Source

Registration, `function_regmatches()`, and shared error state: [source/functions.c](../../source/functions.c). Code-point offset conversion: [source/wcwidth.c](../../source/wcwidth.c). Base85 decoding: [source/ircaux.c](../../source/ircaux.c).
