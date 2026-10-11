# $currchans()

## Synopsis

`$currchans([<server refnum>])`

## Technical

Returns the current channel for each window associated with the selected server. With no argument, it includes windows on all servers. An explicit numeric server reference filters by that server; `-1` selects the server in the current execution context, and `-2` explicitly selects all servers. If the current execution server is the no-server value `-2`, `-1` also selects all servers. Additional arguments are ignored.

This lists one current channel per qualifying window, rather than every channel joined in that window. Windows without a current channel are omitted. Hidden windows are included, as well as windows on live screens. The function reads local window/channel state and does not send an IRC request or require a server registration check.

Each result entry is the double-quoted pair `"<server refnum> <channel>"`, with entries separated by spaces. These quotes are always emitted, regardless of `/xdebug dword`. Entries follow window traversal order: screen window lists, then hidden windows; they are not sorted by server or channel. Window reference numbers are not included.

## Returns

Returns the quoted server/channel pairs, or an empty string if no window qualifies. A server number that matches no window produces an empty string. The server argument uses EPIC's usual integer conversion, not hostname or server-description lookup.

## Examples

If two windows on server 1 have current channels `#epic` and `#unix`, respectively, then `$currchans(1)` returns `"1 #epic" "1 #unix"` in that window order.

```irc
echo All current channels: $currchans()
echo Current server's current channels: $currchans(-1)
```

A window joined to several channels contributes only its elected current channel. [`/window`](../commands/window.md) can change which channel is current.

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). Window traversal and current-channel access are in [source/window.c](../../source/window.c); channel election is in [source/names.c](../../source/names.c).
