# $cofilter()

## Synopsis

`$cofilter(<pattern> <var1> <var2>)`

## Description

This is the complementary operation of [`$copattern()`](copattern.md). It is closely related to [`$filter()`](filter.md), which returns words that do not match a wildcard pattern.

The arguments `<var1>` and `<var2>` are variable names, not the word lists themselves. Their values are read and paired by position. Each word in the first list is tested against `<pattern>`; the corresponding word from the second list is returned only when the first word does **not** match. Matching uses EPIC's case-insensitive wildcard matcher, not regular expressions.

The pattern is always parsed as a double-quoted word: surround it with double quotes if it contains spaces. Those surrounding quotes are removed before matching. The variable-name arguments and both lists use normal function word parsing, which honors double-quoted words when `/xdebug dword` is enabled. In that mode, returned words containing spaces are quoted to preserve their grouping.

Parsing stops when the shorter list runs out of words. Keep the lists the same length when every position should have a counterpart. The variables are not modified, and additional arguments after the two variable names are ignored.

## Practical

Use this function when one variable holds control data and another holds associated data, such as friends' addresses and their access levels. A query against the addresses can then select the corresponding levels.

## Returns

Returns a space-separated list of words from `<var2>` in their original order. Returns an empty string if no pair qualifies, either list is empty, or a required argument is missing.

## Examples

```irc
@ friends = [bob@foo.com tom@bar.com]
@ levels = [20 10]
```

| Expression | Result |
| --- | --- |
| `$cofilter(*@foo.com friends levels)` | `10` |
| `$cofilter(*@bar.com friends levels)` | `20` |
| `$cofilter(*@*.com friends levels)` | Empty string |
| `$cofilter(*@*.net friends levels)` | `20 10` |

## Source

Registered in [source/functions.c](../../source/functions.c) and implemented there through `COPATFUNC`. Word parsing is defined in [include/words.h](../../include/words.h) and [source/words.c](../../source/words.c); result quoting is in [source/ircaux.c](../../source/ircaux.c), and wildcard matching is in [source/reg.c](../../source/reg.c).
