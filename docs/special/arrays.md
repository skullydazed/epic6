# Karll's Array Suite

Ordinary script variables store a scalar string under a name. EPIC also provides **Karll arrays**, named collections of separately stored strings addressed by numeric item numbers. They are built-in storage, distinct from a space-separated word list or the dotted/bracketed names used with ordinary variables.

An array can hold strings containing spaces without your encoding them into a single word list. Its size grows dynamically, subject to memory and implementation limits; it is not literally unlimited. Array names are normalized to uppercase, so their lookup is case-insensitive. Item contents preserve case.

## Creating and changing arrays

Item numbers start at zero. Create an array by setting item `0`:

```text
@ result = setitem(booya 0 random data);
```

This stores the whole remaining text `random data` as one item. Existing items can be overwritten:

```text
@ result = setitem(booya 0 other data);
```

Append only at the next consecutive item number, which equals `numitems(array)`. You cannot create holes by setting an item beyond that point. A nonexistent array must be created at zero, not at an arbitrary later number.

`$setitem(name item text)` returns the following implemented codes:

| Result | Meaning |
| --- | --- |
| `0` | Replaced an existing item. |
| `1` | Created a new array and its item zero. |
| `2` | Appended a new item to an existing array. |
| `-1` | Could not create/find the requested array, or missing arguments/negative item rejected by the wrapper. |
| `-2` | Existing array, but requested item is beyond the append position. |

These are function results, not automatic printed diagnostics. Use nonnegative decimal integer item numbers; the implementation's numeric conversion is permissive and should not be used as input validation.

## Deleting items

Any item can be removed. Items above it shift down by one, so numeric item identities change after deletion:

```text
@ result = setitem(booya 1 new data);
@ result = delitem(booya 0);
```

The array now contains one item: `new data` at item `0`. Deleting the array's sole remaining item deletes the array itself. Deleting the highest item in a larger array merely shortens it.

`$delitem(name item)` returns `0` if an item was deleted, `-1` for an unknown array, or `-2` when no supplied item was valid. It can take several item numbers, but processes them in order against the changing array; deleting lower numbers first changes what later numbers refer to. Delete in descending order when referring to original positions. `$delarray(name)` deletes the whole collection and returns `0` on success or `-1` if absent.

## Item order and sorted index order

Each element has two positions:

- **Item number:** its position in the stored sequence, used by [getitem](../functions/getitem.md). Appending preserves earlier item numbers; overwriting changes the value in place; deletion shifts later items.
- **Index number:** its position in a sorted view, used by [igetitem](../functions/igetitem.md). Sorting compares string bytes case-sensitively with `strcmp`, not numerically or through locale-aware collation. Equal strings are ordered by item number.

Sorted access maintains or refreshes a separate index rather than rearranging item storage. A change in value can change sorted positions without changing item numbers. Both position types start at zero.

```text
@ result = setitem(colors 0 zebra);
@ result = setitem(colors 1 apple);
echo $getitem(colors 0);
echo $igetitem(colors 0);
```

The two reads return `zebra` and `apple`. Both read functions return empty for an unknown array or no valid selected item. Multiple requested positions are joined into one space-separated result, so that combined return is not an unambiguous serialization of strings containing spaces.

`$numitems(name)` returns the item count, and returns `0` for an absent array. Do not treat that result as a distinct unknown-array error. `$getarrays()` lists existing names; `$numarrays()` counts collections.

## Searching

[matchitem](../functions/matchitem.md) and [rmatchitem](../functions/rmatchitem.md) resemble the client's wildcard [match](../functions/match.md) and [rmatch](../functions/rmatch.md) functions:

| Function | Search |
| --- | --- |
| `$matchitem(name pattern)` | Match the supplied wildcard pattern against item values. |
| `$rmatchitem(name text)` | Treat each item value as a wildcard pattern matching the supplied text. |
| `$finditem(name text)` | Find an exact case-sensitive item value through the sorted index. |

All three return an **item number**, not a one-based match position. A valid match can be `0`, so do not test the result solely for truth. Missing arrays return `-1`; no matching value returns `-2`. Wildcard search chooses the highest match score, retaining the first item on a tie. Exact search uses binary search over the sorted view; with duplicate equal values, do not assume it selects the earliest item. `$ifinditem` returns the corresponding sorted index position instead.

## Scope and limits

Karll arrays are shared client-side storage, not local variables scoped to an alias. They do not query a server or require a script package, external program, or optional configure feature. Operations affect the current in-memory client; merely using an array does not save it for a later session.

Appending, deleting, and rebuilding the sorted index have allocation and processing costs. Exact searches use binary search once the sorted index is ready, but an outstanding re-sort can add work. Use the individual function pages for advanced matching, index conversion, and bulk operations rather than assuming every array function has the same error codes or ordering rules.

## Source references

- [source/array.c](../../source/array.c): `set_item`, `get_array`, item deletion, sorted indices, wildcard searches, exact searches, and count/read behavior.
- [include/array.h](../../include/array.h): array function interfaces.
- [source/functions.c](../../source/functions.c): registration of the built-in array functions.
