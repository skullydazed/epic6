# Karll arrays

## What are Karll arrays?

Karll arrays are named collections of string items, named after their contributor. They are C built-in storage, separate from ordinary variables with dotted or bracketed names and from space-separated word lists. An item can contain spaces without being encoded into one word.

Array names are case-insensitive and are parsed as a single space-delimited name. Item contents preserve case. Arrays are shared client-side storage rather than alias-local variables, and their contents are not automatically saved for another session. See also [arrays](arrays.md).

## Adding items to an array

Create an array by setting item `0`. Append at the next consecutive item number, which is the current count; overwrite an existing item by specifying its number:

```irc
@ result = setitem(example 0 zebra)
@ result = setitem(example $numitems(example) apple pie)
@ result = setitem(example 0 orange)
```

The array contains `orange` at item `0` and `apple pie` at item `1`. There are no gaps: an absent array must be created at zero, and an existing array cannot be extended beyond its next append position.

Both `setitem(name item text)` and `usetitem(name item text)` return `0` for replacement, `1` for creating item zero in a new array, or `2` for appending to an existing array. They return `-1` for missing arguments, a negative item, or failure to create the requested array; an item beyond an existing array's append position returns `-2`. Numeric conversion is permissive, so validate untrusted positions yourself.

`usetitem()` defers updating the sorted view. A later operation that needs sorted indices rebuilds them; an ordinary item-number read does not itself require sorting. This can reduce sorting work when making many changes together.

## Getting items from an array

Each item has two positions, both starting at zero:

- **Item number:** its stored sequence position, supplied to `setitem()` and read with `getitem()`.
- **Index number:** its position in the sorted view, read with `igetitem()`.

The sorted view compares string bytes case-sensitively with `strcmp`, not numerically. Identical strings are ordered by item number. Sorting the view does not rearrange the stored items.

After the example above, `$getitem(example 0)` returns `orange`, while `$igetitem(example 0)` returns `apple pie`. Traverse sorted positions using a strict upper bound:

```irc
for (@ i = 0, i < numitems(example), @ i++) {
    echo $igetitem(example $i);
}
```

`numitems(name)` returns `0` for an absent array. Reads return an empty string if the array or selected position is invalid. A stored empty item also reads as empty, so the returned text alone does not prove whether an item exists. Changes to values can change sorted indices; deletion can change item numbers. Do not keep either position as a permanent identity across mutations.

## Finding values in an array

`finditem(name text)` performs exact, case-sensitive lookup and returns an item number. `ifinditem(name text)` returns a sorted index instead. Both return `-1` for an absent array and `-2` when no value matches. Item zero is a valid result: test for a nonnegative result rather than Boolean truth.

`finditems()` and `ifinditems()` are prefix-search interfaces returning one numeric result, not lists of all equal values. Their negative results encode search positions and must not be interpreted using the exact-search functions' simple error convention. Use their individual pages before relying on these advanced forms.

## Using an array to hold wildcard patterns

`rmatchitem(name text)` treats each item as a wildcard pattern and returns the item number of the highest-scoring match against `text`. `getrmatches()` returns all matching item numbers; `igetrmatches()` returns sorted indices. For example, an item containing `*.example.net` can be matched against `host.example.net`.

## Using an array to hold strings to be matched

`matchitem(name pattern)` applies the supplied wildcard pattern to each item, returning the highest-scoring item's number. A tie keeps the first item in stored order. `gettmatch()` returns that item's text instead. `getmatches()` returns all matching item numbers; `igetmatches()` returns all matching sorted indices.

The single-best-match functions return `-1` for an absent array and `-2` for no match. `gettmatch()` also returns those numeric error strings, rather than an empty string. The all-match list functions return empty when the array is absent or nothing matches. These are wildcard searches, not regular-expression searches.

## Using an array with duplicate items

Duplicate values are allowed. Exact binary lookup may select any matching duplicate, not necessarily the first stored item. `ifindfirst()` finds the beginning of the exact duplicate range in sorted order; its implemented failure values are `-1` for absent array or empty search text and `-2` for no match. To process every duplicate, walk that sorted range while comparing exact values, or inspect the individual matching-function interfaces.

## Deleting items from an array

Deleting an item shifts all higher item numbers down by one. If items `0` through `3` exist and item `1` is deleted, the old items `2` and `3` become `1` and `2`. Deleting the sole remaining item destroys the array. `delarray(name)` removes the entire collection.

`delitem(name item)` returns `0` if an item was deleted, `-1` for an absent array, and `-2` when no supplied item was valid. It accepts multiple positions but processes them against the changing array; delete descending original positions to avoid shifting later selections. `delarray()` returns `0` on success and `-1` when absent.

## Functions

| Functions | Purpose |
| --- | --- |
| [setitem](../functions/setitem.md), [usetitem](../functions/usetitem.md) | Create, append, or replace an item; optionally defer sorting. |
| [getitem](../functions/getitem.md), [igetitem](../functions/igetitem.md) | Read by item number or sorted index. |
| [numitems](../functions/numitems.md), [numarrays](../functions/numarrays.md), [getarrays](../functions/getarrays.md) | Count items/arrays or list array names. |
| [matchitem](../functions/matchitem.md), [rmatchitem](../functions/rmatchitem.md), [gettmatch](../functions/gettmatch.md) | Find a best wildcard match. |
| [getmatches](../functions/getmatches.md), [getrmatches](../functions/getrmatches.md), [igetmatches](../functions/igetmatches.md), [igetrmatches](../functions/igetrmatches.md) | List matching positions. |
| [finditem](../functions/finditem.md), [ifinditem](../functions/ifinditem.md), [ifindfirst](../functions/ifindfirst.md) | Exact lookup and duplicate-range start. |
| [finditems](../functions/finditems.md), [ifinditems](../functions/ifinditems.md) | Advanced prefix lookup. |
| [itemtoindex](../functions/itemtoindex.md), [indextoitem](../functions/indextoitem.md) | Convert between position types. |
| [delitem](../functions/delitem.md), [delitems](../functions/delitems.md), [delarray](../functions/delarray.md) | Delete selected items or an array. |

These operations require neither an IRC server nor a script package. Storage and index rebuilds consume memory and processing time; array sizes are subject to implementation and resource limits. Consult each function's page for its own validation and return conventions.

## Source references

- `source/array.c`: name normalization, storage, sorting, search macros, creation, and deletion.
- `include/array.h`: array function interfaces.
- `source/functions.c`: built-in function registration.
- `source/if.c`: C-style `for` condition evaluation.
