# querywin function

## Synopsis

`$querywin(<target> [server refnum])`

## Technical

Returns the user-visible window refnum of a window whose current query target matches `target`. An optional nonnegative server refnum restricts the lookup to windows associated with that server. With no server argument, or any negative value, all servers are searched rather than just the current server.

The target is the first normal function word, with double-quoted grouping when `/xdebug dword` is enabled. The optional server argument is parsed as an integer, not a server name or description; additional arguments are ignored. Comparison is case-insensitive using EPIC's Unicode comparison, not a server-specific nickname casemap.

All windows, including hidden windows, are inspected. The first match in window traversal order is returned. Only each window's current query is tested: an older target retained in its conversation list does not match until made current again. The function does not return a query target given a window number or change the selected window.

## Returns

The matching window's user-visible refnum, or `-1` if no matching current query exists. Missing target input returns the empty string instead. Lookup is local and does not require a connected server or check that the target exists on IRC.

## Examples

```text
/window refnum 2
/query person
/echo $querywin(person)
```

This returns `2` when window 2 exists and `person` is its current query, with no earlier matching window on another server. `$querywin(person 0)` searches only windows associated with server refnum 0.

After `/query person,other`, `other` is current. `querywin(person)` no longer finds that window through its older `person` conversation entry; `querywin(other)` can find it.

## Restrictions

Target strings are interpreted as stored query names. The historical `=nickname` spelling has no special DCC lookup behavior here. See [/query](../commands/query.md) for target selection and [/window](../commands/window.md) for window operations.

## Source

Registration and `function_querywin()`: [source/functions.c](../../source/functions.c). Window traversal, `get_window_equery()`, and user refnums: [source/window.c](../../source/window.c). Case-insensitive comparison: [source/ircaux.c](../../source/ircaux.c).
