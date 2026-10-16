# window query

## Synopsis

```text
/window [<window>] query [<target>[,<target>...]]
/window [<window>] -query
```

## Description

Add targets to the selected window's target list and make them current queries. With no window selector, use the current window. The comma-separated targets are processed left to right, normally making the final target the current query. Previous targets remain in the list, so output associated with them can continue going to this window. See [window add](add.md) for target-list routing and [window target](target.md) for the window's effective target.

The current query takes precedence over the current channel as the window's default conversation target. Setting a query does not send a message, verify that a nickname is online, or join a channel. The eventual sending command and server determine whether a target accepts messages. A query can be set on an invisible window without showing or selecting it.

A target newly claimed by this window is removed from another window that owns it on the same server. Reusing an existing target updates its query priority rather than clearing the other entries. Query selection is based on stored recency counters; [switch_query](../bind/switch_query.md) can cycle these priorities.

## Ending conversations

`/window query` without an argument removes **only the current query** from the target list. The remaining target with the highest query priority becomes current; if none remains, the window falls back to its current channel as its effective target.

`/window -query` removes all target-list entries, including older queries and targets added with `window add`. It reports `All conversations in this window ended`. Neither form parts channels or deletes stored output.

## Target syntax and output

The target list is one argument, with commas separating targets. A double-quoted argument is accepted, but use commas rather than spaces to separate targets. Additional words are processed as subsequent window operations on the selected window.

`.` resolves to the last nickname you sent a message to on the window's server; if none exists, EPIC reports `You have not messaged anyone yet`. `*` resolves to that window's current channel, or reports `You are not on a channel`. The resolver also supports a comma as the last received-message nickname, but a comma is this operation's list separator, so it is not a reliable standalone shorthand here. Process targets beginning with `%` are accepted without checking that the process exists; acceptance alone does not create a process.

Adding a new target reports `Added <target> to window target list`; removing one reports `Removed <target> from window target list`. Reusing an existing target need not produce an addition message. Changes request status updates. Removing a query when none exists is silent. A target that fails shorthand resolution is skipped while processing continues.

## Examples

```text
/window 3 query alice,bob
/window 3 query alice
/window 3 query
/window 3 -query
```

Add two conversations in window 3, select Alice again while retaining Bob, remove the current conversation, or end all conversations in that window. To query the last nickname you messaged:

```text
/window query .
```

## Source references

- [source/window.c](../../source/window.c): `windowcmd_query`, `get_target_special`, `add_window_target`, `remove_window_target`, `recheck_queries`, `get_window_equery`, and `get_window_target` implement list changes, shorthand resolution, recency, and target precedence.
- [source/screen.c](../../source/screen.c): `add_to_screen` routes nickname-associated output through window target lists before level-based routing.
