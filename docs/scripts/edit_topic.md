# edit_topic

## Synopsis

```text
/load edit_topic
/edit_topic
```

## Description

This script defines the `edit_topic` alias, which puts the current channel's topic into the input line for editing. It queries the server rather than using a locally cached topic, waits for a server round trip, then replaces the input line with:

```text
/topic <returned-topic>
```

Nothing is submitted automatically. Edit the line and press Enter when ready. The script accepts no channel selector or other options; supplied arguments are not used. Start it in a connected window with a current channel. The underlying [topic](../commands/topic.md) command reports `You are not on a channel in this window.` if none is available.

The server determines whether the query and any later change are permitted. Channel privileges, topic protection, topic length limits, and the server's replies govern the result. A query error does not stop the alias from eventually replacing the input line with an empty-topic draft.

## Editing behavior and limits

The alias temporarily saves and removes the installed numeric `331`, `332`, and `333` hooks using [stack](../commands/stack.md). It installs suppressing handlers for those replies, captures the topic text from numeric `332`, and restores the previous handlers after preparing the input line. Numeric `331` (no topic) leaves the captured text empty; numeric `333` metadata is hidden. Other errors are not handled by this script.

Its handlers match all replies of those numeric types, not just the requested channel or server. Another topic reply arriving during the wait can overwrite the captured topic. An interrupted wait can also prevent normal hook restoration. Do not overlap several calls expecting independent capture.

The [wait](../commands/wait.md) operation continues processing events while waiting for a server round trip; slow or missing replies can delay it. The script does not lock the channel selection or bind the prepared command to the original channel. Confirm the current channel before submitting the draft, especially if you changed windows while waiting.

Preparing the draft uses `parsekey erase_line`, so it replaces any existing input text and moves that text to the cut buffer when nonempty. It then uses `xtype -literal` to insert the command and topic as input characters rather than execute them. This is an input-line editor helper, not a function returning a topic string.

The draft has no explicit channel argument. A topic beginning with a recognized channel prefix, such as `#` or `&`, can therefore be parsed as a channel selector when submitted. A leading hyphen can invoke the topic command's clearing syntax. Insert the intended channel explicitly before the text to preserve such topics, for example:

```text
/topic #example # Welcome to this channel
```

If there is no topic, or no usable reply was captured, the prepared line is just `/topic `; pressing Enter queries the topic again rather than clearing it. Use `/topic -#example` deliberately when you intend to clear a topic. The script does not check whether another user changes the topic between retrieval and submission.

## Example

```text
/load edit_topic
/edit_topic
```

Run this from the channel window, revise the prepared command, check its target and text, then press Enter to request the change.

## History

This script first appeared in EPIC4-1.0.

## Source references

- [script/edit_topic](../../script/edit_topic): temporary numeric handlers, topic query, wait, and draft insertion.
- [source/commands.c](../../source/commands.c): `e_topic`, `waitcmd`, and literal `xtype` insertion.
- [source/numbers.c](../../source/numbers.c): topic numeric handling and numbered hook arguments.
- [source/input.c](../../source/input.c): `erase_line` clears input and updates the cut buffer.
- [source/hook.c](../../source/hook.c) and [source/server.c](../../source/server.c): hook stacks and server round-trip waiting.
