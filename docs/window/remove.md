# window remove

## Synopsis

```text
/window [<window>] remove <target>[,<target>...]
```

## Description

Remove one or more entries from the selected window's target list, ending that list's claim on their output. With no window selector, use the current window. This applies to entries created by either [window add](add.md) or [window query](query.md), including the current query and older conversations.

The argument is one comma-separated, whitespace-delimited token. Names are removed literally from this window's list; unlike `window add`, this operation does not resolve `*` or `.` shorthands. Entries belonging only to another window are unaffected. A target not present in this window's list is silently ignored.

After removing an entry, EPIC recalculates the current query from the remaining targets' query-priority counters. Removing the current query can therefore select another conversation. If the list becomes empty, the window has no query and its effective target can fall back to its current channel.

For ordinary nicknames, future output no longer follows this window's removed target-list claim. Other routing rules, including explicit destinations, level claims, and fallbacks, can still send output here; removal does not suppress messages. A channel-name entry can be removed from the target list, but channel output follows the channel's own window association. This operation does not move or part a joined channel; use [window channel](channel.md) to move it.

Stored output, lastlog, and saved logfiles are unchanged. No IRC request is sent, and removing a target does not close a process or connection associated with that target name.

## Output and arguments

Each removed entry reports `Removed <target> from window target list`. Removal requests status updates. With no argument, the operation silently does nothing; it does not clear or list all targets. Use `/window -query` to clear the target list.

There are no other options. Additional words are processed as subsequent window operations. `/window -remove` is a no-op. The operation works on invisible windows without showing them or changing input selection.

## Examples

```text
/window remove joebob
/window 3 remove alice,bob
```

Remove Joebob's target-list entry from the current window, or Alice and Bob from window 3. To remove a literal channel-name entry as well:

```text
/window remove #blah,joebob
```

This does not change which window owns the joined channel `#blah`.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_remove` parses the literal list; `remove_window_target` removes entries and reports them; `recheck_queries`, `get_window_equery`, and `get_window_target` update query choice and effective-target fallback.
- [source/screen.c](../../source/screen.c): `add_to_screen` checks channel associations separately from nickname target lists and level routing.
