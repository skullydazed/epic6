# $joinstr()

## Synopsis

`$joinstr(<separator> <var1> [<var2> ...])`

## Description

Joins the first word of each named variable with the separator. The arguments are variable names, not their expanded values. More than two variables can be supplied.

The current implementation forms only one joined group: it does not continue through subsequent words in the lists. An empty or undefined variable supplies an empty placeholder, and a separator is still inserted between every pair of variable positions. One variable returns its first word; a separator without variable names returns the empty string. Missing separator input also returns the empty string.

The separator, variable names, and words taken from their values use function-argument parsing. Double-quoted word grouping and dequoting depend on [xdebug](../commands/xdebug.md) `dword`. With `dword` enabled, the returned group is quoted when it contains a space; with it disabled, the joined text is returned without that grouping protection.

Values use ordinary EPIC variable lookup, including local variables before global values and fallback to built-in values or environment variables. The source variables are not consumed or changed: the function reads copies. Variable lookup can still invoke normal lookup behavior, including lazy-loading a stub.

## Practical

To combine a nickname and userhost into a `nick!user@host` string:

```text
@nicks = [one two three]
@userhosts = [foo@baz.com boo@ya.com bar@oof.com]
@nuh = joinstr(! nicks userhosts)
echo $nuh
```

This outputs `one!foo@baz.com`; the remaining list words are not processed. Use iteration when you need one joined result per list position. For matching userhost patterns to corresponding nicknames, see [copattern](copattern.md).

## Returns

The joined first-word group, or the empty string when no nonempty result is formed. There is no separate error sentinel.

For example, with `left` containing `one two` and an undefined `missing` variable, `$joinstr(! left missing)` returns `one!`.

## History

The function first appeared in EPIC4-1.1.8. Support for more than two variable arguments first appeared in EPIC4-1.1.15.

## Source

Registered and implemented as `function_joinstr` in [source/functions.c](../../source/functions.c). Variable lookup is in [source/alias.c](../../source/alias.c), function-word parsing is defined in [include/words.h](../../include/words.h), and output-word quoting is in [source/ircaux.c](../../source/ircaux.c).
