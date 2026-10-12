# gettmatch function

## Synopsis

`$gettmatch(<array> <pattern>)`

## Technical

Returns the stored text of the best match of `pattern` within the named array of strings. This differs from [$getmatches()](getmatches.md), which returns the item numbers of all matches. [$matchitem()](matchitem.md) makes the same best-match selection but returns its storage item number.

The supplied pattern is matched against each complete item value with EPIC's case-insensitive wildcard matcher, not a regular expression. The item values are treated as text, not patterns. The highest positive wildcard-match score wins. Items are examined in ascending storage item order, and a tie retains the first matching item; a longer value does not inherently outrank a shorter one.

The array name is the first ordinary, unquoted word and is case-insensitive. The entire remaining text is the pattern, including spaces and literal quotation marks. Do not quote a pattern merely to group spaces. This parsing does not depend on `/xdebug dword`, and extra words are part of the same pattern.

The function does not change array contents or sorted indexes. These are named arrays created with [$setitem()](setitem.md), separate from `/assign` structures.

## Returns

The selected item's complete text, without added quoting. Returns `-1` if the array does not exist or input is missing, and `-2` if the array exists but no item matches. No diagnostic accompanies these results.

An omitted pattern is tested as an empty pattern; it can select an empty stored value. A successful empty value returns empty. Stored values equal to `-1` or `-2` are indistinguishable from failure codes in this function's result; use `$matchitem()` if you need to distinguish success before retrieving the text.

## Examples

Given this array:

```irc
@ setitem(sample 0 apple pie)
@ setitem(sample 1 APPLE)
@ setitem(sample 2 pear)
```

| Expression | Result |
| --- | --- |
| `$gettmatch(sample apple*)` | `apple pie` (the first of two tied matches) |
| `$gettmatch(sample apple)` | `APPLE` |
| `$gettmatch(sample *pear*)` | `pear` |
| `$gettmatch(sample *orange*)` | `-2` |

## Source

Registered in [source/functions.c](../../source/functions.c). The active `MATCHITEM` wrapper, text return, array lookup, and tie handling: [source/array.c](../../source/array.c). Ordinary-word parsing: [include/words.h](../../include/words.h). Wildcard matching and scores: [source/reg.c](../../source/reg.c).
