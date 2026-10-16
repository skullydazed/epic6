# window stack

## Synopsis

```text
/window [<window>] stack
```

## Description

Display the saved window stack belonging to the selected visible window's screen. With no window selector, use the current window's screen. Entries are added by [window push](push.md) and listed from newest to oldest. Duplicate pushes appear as duplicate rows.

EPIC prints `Window stack:` followed by a summary of each entry's current window state: refnum, server nickname, window name, current channel, current query, server name, and output-routing level mask. Hidden windows have `Hidden` appended. The format is the same row format as [window list](list.md), but no column-heading row is printed. An empty stack prints only `Window stack:`.

The stack saves internal window references rather than snapshots. Renaming or [renumbering](number.md) a window is reflected in the listing, and hiding it does not remove its entry. If a saved window no longer exists, its row instead reports `Window <internal-number> does not exist`. Listing does not remove that stale entry or otherwise change the stack.

Use [window pop](pop.md) to consume entries. Its current implementation drains the whole stack rather than removing just one valid entry; see that operation for selection and hidden-window limitations.

## Formatting and arguments

Nicknames, query targets, and server names are truncated to 12, 9, and 10 bytes respectively. The name column is at least 4 bytes wide and expands for the longest saved window name; an unnamed placeholder can therefore be truncated. [set channel_name_width](../set/channel_name_width.md) controls the channel column, using 12 when that setting is zero. These formatting limits do not alter the underlying values.

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations on the same target. Selecting an invisible window produces no listing and stops subsequent operations because it has no screen stack. `/window -stack` is a no-op for a visible target.

Listing does not change visibility, input selection, geometry, or stored output, and sends no IRC request.

## Examples

```text
/window push
/window stack
/window 3 stack
```

Save the current window, display its screen's stack, or display the stack of the screen containing visible window 3.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_stack` lists entries without deleting them; `windowcmd_push` records internal window references; `list_a_window` and `WIN_FORM` define row output and missing-window diagnostics; `windowcmd_pop` consumes the stack.
