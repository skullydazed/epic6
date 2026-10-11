# chanwin function

## Synopsis

```text
$chanwin()
$chanwin(window-refnum-or-name)
$chanwin(channel [server-description])
```

## Technical

`chanwin()` and [`winchan()`](winchan.md) are the same function. They convert between a window and its current channel, or between any tracked channel and the window that owns it.

With no argument, the result is the current window's current channel. With a window reference number, name, or UUID, it returns that window's current channel, or the empty string if the window is unknown or has no current channel. Reference number `0` selects the current window. Named and UUID lookup are case-insensitive and can find hidden windows.

If the first argument is recognized as a channel name, the function instead returns its owning window's user reference number. This includes channels that are not that window's current channel. It returns `-1` if the channel or owning window cannot be found. Channel-name recognition uses the current execution server's advertised `CHANTYPES`, or the client's fallback channel prefixes.

In the channel form, omitted server information uses the current execution server (`from_server`), with primary-server fallback through channel lookup when that context has no server. A supplied server description is resolved against existing server records using the same description lookup as [`server`](../commands/server.md); a server number or matching server name can be used. No new server is added and no connection is made. The entire remainder after the channel name is passed to that lookup, rather than separately parsing one server word.

The first argument uses function word parsing controlled by [`xdebug dword`](../commands/xdebug.md). Later arguments in the window form are ignored. `*` is not a special default-channel selector for this function: unless it is recognized as a channel prefix by the server, it is looked up as a window description.

## Practical and returns

Use the channel form to find where a joined channel is attached, even if another channel is currently selected in that window. Use the window form to obtain its currently selected channel. These are local queries; they neither join channels nor change the current window or channel.

The return type depends on the form: a channel string or empty string for window lookup, and a numeric user reference or `-1` for channel lookup. Pass the desired channel explicitly in an [`on`](../commands/on.md) handler; a no-argument call refers to the current window, which need not be the event's channel window.

## Examples

If window 2 currently has `#epic` selected, and owns that channel on the execution server:

```text
$chanwin(2)             returns #epic
$chanwin(#epic)         returns 2
$chanwin(#epic 0)       looks up #epic on server 0
$chanwin(0)             returns the current window's current channel
```

## History

`chanwin()` first appeared in EPIC4pre0.034. It was swapped with `winchan()` in EPIC4pre1.027; the functions were unified in EPIC4pre1.041. Support for non-current channels first appeared in EPIC4pre2.506.

## Source

[source/functions.c](../../source/functions.c) registers both names and implements `function_winchan()`. [source/names.c](../../source/names.c) implements channel ownership lookup, [source/window.c](../../source/window.c) resolves window descriptions, [source/server.c](../../source/server.c) resolves server descriptions, and [source/parse.c](../../source/parse.c) recognizes channel prefixes.
