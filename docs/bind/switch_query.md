# switch_query

## Synopsis

```text
/bind <key> switch_query
```

## Description

When the current window has multiple query targets, cycle through them to choose the current query. This built-in action selects the least recently selected target whose selection counter is older than the current query's counter, then records that target as the newest selection. Under ordinary query selection, repeated invocations rotate through the window's targets in selection order rather than alphabetical order.

Only targets already in the current window's target list are considered. The action does not move targets between windows or change input focus. With no eligible older target, including the usual cases of zero or one query target, it makes no change.

A successful switch updates the window's status and raises the `switch_query` [on](../commands/on.md) event with the window reference number, old query, and new query. The current query takes precedence over the current channel as the destination for ordinary input text. The action itself sends no message or IRC request and does not check whether a target nickname exists.

The action takes no arguments or options; extra text supplied with the binding is ignored. It has no default key assignment.

## Example

```text
/query alice,bob
/bind ^X^Q switch_query
```

The [query](../commands/query.md) command adds both targets to the current window and makes `bob` current. Press Control-X followed by Control-Q to switch to `alice`; pressing it again switches back to `bob`.

See [bind](../commands/bind.md) for key sequence syntax, [window query](../window/query.md) to manage the target list, and [switch_channels](switch_channels.md) to cycle channels.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default key assignments.
- [source/window.c](../../source/window.c): `switch_query`, `get_window_equery`, `add_window_target`, and `get_window_target` implement selection counters, status updates, and query precedence.
- [source/hook.c](../../source/hook.c): registration of the three-argument `switch_query` event.
