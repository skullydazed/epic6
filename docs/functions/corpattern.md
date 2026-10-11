# $corpattern()

## Synopsis

`$corpattern(<string> <var1> <var2>)`

## Description

This reverses the matching direction of [`$copattern()`](copattern.md) and is the complementary operation of [`$corfilter()`](corfilter.md). It is closely related to [`$rpattern()`](rpattern.md), which returns wildcard patterns that match a literal string.

The arguments `<var1>` and `<var2>` are variable names. The first variable contains a word list of wildcard patterns; the second contains associated words. Each pattern in the first list is tested against the same literal `<string>`. If it matches, the word at the corresponding position in the second list is included in the result. Matching is case-insensitive and uses EPIC wildcards, not regular expressions. Wildcard characters in `<string>` are ordinary text, not patterns.

The string argument is always parsed as a double-quoted word: use surrounding double quotes when it contains spaces. Those quotes are removed before matching. The variable names and both lists use normal function word parsing, which honors double-quoted words when `/xdebug dword` is enabled. In that mode, returned words containing spaces are quoted to preserve their grouping.

Parsing stops when the shorter list runs out of words. Keep the lists the same length when every position should have a counterpart. The variables are not modified, and additional arguments after the two variable names are ignored.

## Returns

Returns a space-separated list of words from `<var2>` corresponding to the matching patterns in `<var1>`, in their original order. Returns an empty string if no pair qualifies, either list is empty, or a required argument is missing.

## Examples

```irc
@ friends = [ooga!bob@*.foo.com *!tom@*.com]
@ levels = [20 10]
```

| Expression | Result |
| --- | --- |
| `$corpattern(ooga!bob@lace.foo.com friends levels)` | `20` |
| `$corpattern(hype!tom@bar.com friends levels)` | `10` |
| `$corpattern(nobody!user@example.net friends levels)` | Empty string |

## Source

Registered in [source/functions.c](../../source/functions.c) and implemented there through `COPATFUNC`. Word parsing is defined in [include/words.h](../../include/words.h) and [source/words.c](../../source/words.c); result quoting is in [source/ircaux.c](../../source/ircaux.c), and wildcard matching is in [source/reg.c](../../source/reg.c).
