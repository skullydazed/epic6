# before function

## Overview

```text
$before([count] chars text)
```

## Technical

`before()` returns the part of `text` before the `count`th occurrence of any character in `chars`, excluding the matched character. `chars` is a set of alternatives, not a substring, and comparisons are exact and case-sensitive. Quote it with double quotes when it contains spaces; those surrounding quotes do not count as delimiters. The remaining argument text is searched as a whole.

The default count is 1. A positive count searches from the beginning; a negative count searches from the end, including a delimiter at the very end. If there are too few matches or required arguments are missing, the result is the empty string. Matching the first character also returns the empty string because there is no preceding text.

A leading caret in `chars` inverts the set: `^abc` matches characters other than `a`, `b`, or `c`. A caret elsewhere is an ordinary delimiter. Matching uses UTF-8 code points; a multibyte delimiter is excluded in full, and the preceding text is returned unchanged. Use valid UTF-8 for predictable matching.

## Restrictions

The first word is interpreted as a count only when its `strtol()` conversion produces a nonzero value. Conversion accepts a numeric prefix and uses base detection, including hexadecimal and leading-zero octal notation. A zero conversion makes that first word the character set with the default count of 1. Thus zero is not a supported zero-count operation: `$before(0 ! a0b)` searches for `0` in `! a0b` and returns `! a`. Avoid an all-numeric delimiter set that could be mistaken for a count.

See [`after()`](after.md) for the following text and [`beforew()`](beforew.md) for word-based extraction. `after()` currently has different backward-search and multibyte-suffix limitations; these do not apply to `before()`'s prefix construction.

## Defined behaviors

| Expression | Return value |
| --- | --- |
| `$before(1 ! hop!user@host)` | `hop` |
| `$before(1 / /usr/local/bin/epic)` | Empty string: nothing precedes the first slash |
| `$before(-1 / /usr/local/bin/epic)` | `/usr/local/bin` |
| `$before(-2 / /usr/local/bin/epic)` | `/usr/local` |
| `$before(5 / /usr/local/bin/epic)` | Empty string: fewer than five slashes |
| `$before(/ /usr/local/bin/epic)` | Empty string: count defaults to 1 |
| `$before(0 ! hop!user@host)` | Empty string: no `0` in the remaining text |
| `$before(1 @ /usr/local/bin/epic)` | Empty string: no matching delimiter |
| `$before(/usr/local/bin/epic)` | Empty string: missing text |
| `$before()` | Empty string |
| `$before(-1 / a/b/)` | `a/b` |
| `$before(1 é café noir)` | `caf` |

## History

This function first appeared in “+10” (post-ircII, pre-EPIC). Support for the count argument first appeared in “+11”.

## Source

[source/functions.c](../../source/functions.c) registers `before` and implements `function_before()`. [source/words.c](../../source/words.c) implements search direction. [source/ircaux.c](../../source/ircaux.c) implements numeric conversion and code-point set matching, using decoding helpers from [source/wcwidth.c](../../source/wcwidth.c).
