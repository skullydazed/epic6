# The client uses too much memory

High memory use can come from retained output, scripts, channel state, or other live resources as well as from a memory leak. Before reporting a leak, compare the growth with what the client is retaining and what it is doing.

## Common causes

### A window left in scrollback or hold mode

Normal scrollback trimming is suspended while a scrollback view is active, or while a held view has more than a full page of output waiting. A busy window can therefore grow beyond its configured scrollback size. Merely enabling hold mode without a backlog does not have this effect.

Catch up with the output or end the scrollback view; a later normal output-and-trim pass can then enforce the limit. [Scroll_end](../bind/scroll_end.md) ends the scrollback view. [Window flush_scrollback](../window/flush_scrollback.md) explicitly discards the display buffer when that is what you intend; it does not itself delete the associated lastlog entries or saved log files.

### Large scrollback buffers

[Window scrollback](../window/scrollback.md) limits physical display rows after wrapping, not original messages. Long output can occupy several rows, and each retained row has allocation and bookkeeping overhead in addition to its text. There is no fixed bytes-per-row estimate applicable to every build and workload.

Lowering the limit does not immediately trim the existing buffer; normal later trimming applies it. The limit is at least twice the window's output height. Changing [set scrollback](../set/scrollback.md) updates all existing windows, whereas `window scrollback` changes one window.

### Large lastlogs

Lastlog retains original output entries, separate from the wrapped scrollback rows. An entry can contain much more text than one visible screen row. [Window lastlog](../window/lastlog.md) controls the counted lastlog size; entries outside the window's lastlog-level selection are not included in that count, so it is not a strict cap on every retained entry.

Lowering a window's lastlog limit trims immediately and schedules rebuilding scrollback from the remaining data. This can discard history; increasing the limit later cannot recover deleted output. Saved log files are separate from these in-memory buffers.

### Many windows

Each window has its own display state and output retention. Modest per-window limits can still add up across many windows. Hidden windows also retain data; hiding a window does not free its buffers. Inspect windows individually before assuming that the currently visible window accounts for the growth.

### Scripts, aliases, and assignments

Alias bodies and variable values occupy memory, and scripts can accumulate arrays, hooks, timers, and queues. A script file's size is not a reliable measurement of its runtime footprint: parsed definitions, copied strings, generated values, and queued work add overhead.

[Unload](../commands/unload.md) can remove package-tagged script resources, but it is not a universal undo operation. Untagged resources and independently stored Karll arrays can remain. Use the script's documented cleanup procedure where provided; [delarray](../functions/delarray.md) removes an entire array when it is no longer needed.

### Channels and servers

EPIC stores channel membership, nicknames, modes, and cached userhost information. Many channels, large membership lists, and multiple server connections increase live state. The memory cost is data-dependent, not a fixed number per channel or per server. Disconnection, leaving a channel, and deleting a server-list entry are different operations; do not assume that closing a connection removes its configuration entry.

## Adjusting retention

For example, these commands change the current window's limits:

```irc
/window scrollback 500
/window lastlog 500
```

The numbers are counts, not byte budgets. The window-height minimum and held/scrollback exceptions still apply. Query the current values by omitting the numbers, and consult the individual pages before using destructive flush or cleanup operations.

## Why a process monitor may not shrink

EPIC frees discarded entries through its allocator wrappers, but the operating system's reported process memory need not fall immediately. The C allocator may retain freed blocks for reuse, and resident memory, virtual address space, and live allocated text measure different things. Results vary by allocator and platform; freeing client data does not guarantee a particular change in `top` or `ps`, nor that retained pages will automatically be paged out.

A useful leak report describes the EPIC build, loaded scripts, window count, retention limits, and whether windows are holding or scrolled back. Include the operation that causes repeatable growth and the cleanup that was attempted. Growth that continues under a repeatable workload after retained state has stabilized is more informative than one large process-memory reading. See [debugging](debugging.md) for diagnostic controls.

## Source references

- `source/window.c`: `trim_scrollback()`, per-window buffers, size settings, and view state.
- `source/lastlog.c`: entry storage, counted-size trimming, and removal.
- `source/alias.c`, `source/array.c`, and `source/timer.c`: script definitions, arrays, and queued timers.
- `source/names.c` and `source/server.c`: channel/nickname state and server objects.
- `source/ircaux.c`: `really_new_free()` and allocator wrappers.
