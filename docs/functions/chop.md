# chop function

## Overview

```text
$chop([number] text)
```

## Summary

`chop()` returns the initial part of `text`, excluding the final `number` UTF-8 code points. The default number is 1. It returns a new string and does not update a variable unless the caller assigns the result.

## Arguments and restrictions

An initial count is recognized only when converting the beginning of the argument string with `strtol()` produces a nonzero value. The count word is then consumed and converted with `strtoimax()`, using base detection, before being stored as an `int`. Decimal, hexadecimal, and leading-zero octal notation are accepted; a numeric prefix can also be accepted with trailing nonnumeric characters. Use a positive count within the platform's `int` range.

If the initial conversion is zero, the entire argument is treated as text and one code point is removed. Thus `$chop(0 abc)` returns `0 ab`; zero does not mean “remove nothing.” Similarly, text beginning with a nonzero numeric prefix can be mistaken for a count. A negative count is converted to an unsigned removal length internally and returns the empty string for ordinary input; it does not remove characters from the beginning.

Removing at least as many code points as the text contains returns the empty string. Missing input or a count with no following text also returns the empty string.

Counting is by code points, not bytes, screen columns, or user-perceived characters. A combining mark can be removed independently of the letter it modifies. Formatting control characters are counted rather than interpreted as zero-width styling. Use valid UTF-8: the fast length counter and backward decoder are not a validation interface, and malformed bytes can produce unreliable results.

The text remainder is not parsed into words or dequoted by `chop()`. Spaces and quotes present in that remainder are part of the text; normal script expansion occurs before the call. Parsing a recognized count uses the function word mode controlled by [`xdebug dword`](../commands/xdebug.md).

## Examples

| Expression | Return value |
| --- | --- |
| `$chop(5 abcdef)` | `a` |
| `$chop(2 fnord)` | `fno` |
| `$chop(fnord)` | `fnor` |
| `$chop(1 café)` | `caf` |
| `$chop(8 abc)` | Empty string |
| `$chop(-1 abc)` | Empty string |
| `$chop(0 abc)` | `0 ab` |
| `$chop()` | Empty string |

## History

The `chop()` function first appeared in EPIC4pre1.042.

## Source

[source/functions.c](../../source/functions.c) registers `chop` and implements `function_chop()`. [source/ircaux.c](../../source/ircaux.c) implements count detection and the removal helper. [source/wcwidth.c](../../source/wcwidth.c) supplies code-point counting and backward decoding, and [include/functions.h](../../include/functions.h) defines count-word conversion.
