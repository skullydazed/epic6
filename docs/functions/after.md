# after function

## Overview

```text
$after([count] chars text)
```

## Technical

`after()` returns the part of `text` after the `count`th occurrence of any character in `chars`. The delimiter is excluded from the result. Characters are matched exactly and case-sensitively; `chars` is a set of alternatives, not a substring. Quote `chars` with double quotes when it contains spaces; those surrounding quotes do not count as delimiters. The remaining argument text is searched as a whole.

The default count is 1. A positive count searches from the beginning; a negative count searches backward. If there are too few matches, or required arguments are missing, the result is the empty string. A successful match with no following text also returns the empty string.

A leading caret in `chars` inverts the set: `^abc` matches characters other than `a`, `b`, or `c`. A caret elsewhere in the set is an ordinary delimiter.

## Restrictions

The first word is interpreted as a count only when its `strtol()` conversion produces a nonzero value. Conversion accepts a numeric prefix and uses base detection, including hexadecimal and leading-zero octal notation. If the conversion is zero, that first word becomes `chars` and the count defaults to 1. Thus an explicit zero is not a supported zero-count operation: `$after(0 ! a0b)` searches for `0` in `! a0b` and returns `b`. Use a nonzero count and avoid an all-numeric delimiter set that could be mistaken for one.

Backward searches currently begin before the final byte of `text`, so a delimiter at the very end is skipped. For example, `$after(-1 / a/b/)` returns `b/`, rather than the empty suffix after the last slash.

The search compares UTF-8 code points, but the returned suffix starts one byte after the match's first byte. A multibyte delimiter can therefore leave trailing bytes of that character in the result, producing invalid UTF-8. Use ASCII delimiters for reliable suffix extraction; the surrounding text can still contain UTF-8.

## Defined behaviors

| Expression | Return value |
| --- | --- |
| `$after(1 ! hop!user@host)` | `user@host` |
| `$after(1 / /usr/local/bin/epic)` | `usr/local/bin/epic` |
| `$after(-1 / /usr/local/bin/epic)` | `epic` |
| `$after(-2 / /usr/local/bin/epic)` | `bin/epic` |
| `$after(5 / /usr/local/bin/epic)` | Empty string: fewer than five slashes |
| `$after(/ /usr/local/bin/epic)` | `usr/local/bin/epic`: count defaults to 1 |
| `$after(0 ! hop!user@host)` | Empty string: no `0` in the remaining text |
| `$after(1 @ /usr/local/bin/epic)` | Empty string: no matching delimiter |
| `$after(/usr/local/bin/epic)` | Empty string: missing text |
| `$after()` | Empty string |

See also [`before()`](before.md) for text preceding a match and [`afterw()`](afterw.md) for word-based extraction.

## History

This function first appeared in “+10” (post-ircII, pre-EPIC). Support for the count argument first appeared in “+11”.

## Source

[source/functions.c](../../source/functions.c) registers `after` and implements `function_after()`. [source/words.c](../../source/words.c) implements search direction, and [source/ircaux.c](../../source/ircaux.c) implements numeric conversion and code-point set matching.
