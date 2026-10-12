# $matchitem()

## Synopsis

`$matchitem(<array> <pattern>)`

## Technical

Treats the items in a named array as literal strings and returns the storage item number of the string best matched by the supplied wildcard pattern. It does not return the matching text; [$gettmatch()](gettmatch.md) returns that text, and [$getitem()](getitem.md) retrieves text by item number.

The highest positive wildcard-match score wins. Items are examined in ascending storage item order, starting at zero, and a tie retains the first matching item. A longer item value does not inherently have a higher score. Matching uses the same case-insensitive, byte-based wildcard matcher as [$match()](match.md), including its wildcard syntax and limitations.

The array name is the first ordinary, unquoted word and is case-insensitive. The entire remaining text is the pattern, including spaces and literal quotation marks. Do not quote a pattern just to group spaces. This parsing does not depend on `/xdebug dword`; additional words belong to the pattern.

## Practical

This function is analogous to `$match()`, with an array's complete item values taking the place of the word list. Unlike `$match()`, successful item numbers count from zero. [$rmatchitem()](rmatchitem.md) reverses the direction: array items are patterns matched against supplied text, analogous to [$rmatch()](rmatch.md).

These are named arrays created with [$setitem()](setitem.md), separate from `/assign` structures. The function does not change array values or sort their indexes. Its result is a storage item number, not a sorted index number.

## Returns

- A nonnegative storage item number on success, including `0` for the first item.
- `-1` when the array is not found or the array argument is missing.
- `-2` when the array exists but no item matches.

An omitted pattern is tested as an empty pattern and can match an empty stored value. No separate error result distinguishes an invalid pattern from an ordinary failure to match. Check for a nonnegative result before retrieving an item.

## Examples

Given this array:

```irc
@ setitem(sample 0 apple pie)
@ setitem(sample 1 APPLE)
@ setitem(sample 2 pear)
```

| Expression | Result |
| --- | --- |
| `$matchitem(sample apple*)` | `0` (the first of two tied matches) |
| `$matchitem(sample apple)` | `1` |
| `$matchitem(sample apple pie)` | `0` (spaces are part of the pattern) |
| `$matchitem(sample *pear*)` | `2` |
| `$matchitem(sample *orange*)` | `-2` |
| `$matchitem()` | `-1` |

Registration: [source/functions.c](../../source/functions.c). The active `MATCHITEM` wrapper and array lookup: [source/array.c](../../source/array.c). Wildcard matching and scores: [source/reg.c](../../source/reg.c). Ordinary-word parsing: [include/words.h](../../include/words.h).
