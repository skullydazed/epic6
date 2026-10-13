# $rmatchitem()

## Synopsis

`$rmatchitem(<array> <literal string>)`

## Technical

Treats the items in a named array as wildcard patterns and returns the storage item number of the pattern that best matches the supplied literal string. It does not return the pattern text; use [$getitem()](getitem.md) to retrieve that text by item number.

The highest positive wildcard-match score wins. Items are examined in ascending storage item order, starting at zero, and a tie retains the first matching item. Matching uses the same case-insensitive, byte-based wildcard matcher as [$rmatch()](rmatch.md), with the wildcard syntax and limitations described for [$match()](match.md).

The array name is the first ordinary, unquoted word and is case-insensitive. The entire remaining text is the literal string, including spaces and quotation marks. Do not quote text just to group spaces. This parsing does not depend on `/xdebug dword`.

## Practical

This function is analogous to `$rmatch()`, with the array's complete item values taking the place of the pattern list. Unlike `$rmatch()`, successful item numbers count from zero. It reverses [$matchitem()](matchitem.md), which treats array items as literal strings and matches them against one supplied pattern.

These are named arrays created with [$setitem()](setitem.md), separate from `/assign` structures. The function does not change array values or sort their indexes. Its result is a storage item number, not a sorted index number.

## Returns

- A nonnegative storage item number on success, including `0` for the first item.
- `-1` when the array is not found or the array argument is missing.
- `-2` when the array exists but no item matches.

An omitted literal string is tested as empty text and can match an empty stored pattern or `*`. No separate error result distinguishes an invalid pattern from an ordinary failure to match. Check for a nonnegative result before retrieving an item.

## Examples

Given this array:

```irc
@ setitem(masks 0 o*)
@ setitem(masks 1 on*)
@ setitem(masks 2 on*)
@ setitem(masks 3 t*)
```

| Expression | Result |
| --- | --- |
| `$rmatchitem(masks one)` | `1` (the more specific pattern wins; the tie with item 2 retains item 1) |
| `$rmatchitem(masks orange)` | `0` |
| `$rmatchitem(masks two words)` | `3` (the complete remaining text is matched) |
| `$rmatchitem(masks apple)` | `-2` |
| `$rmatchitem()` | `-1` |

Registration: [source/functions.c](../../source/functions.c). The active `MATCHITEM` wrapper and array lookup: [source/array.c](../../source/array.c). Wildcard matching and scores: [source/reg.c](../../source/reg.c). Ordinary-word parsing: [include/words.h](../../include/words.h).
