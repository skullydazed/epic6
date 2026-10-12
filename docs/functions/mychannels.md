# $mychannels()

## Synopsis

```text
$mychannels()
$mychannels(<server refnum>)
$mychannels(#<window refnum>)
$mychannels(<window name or UUID>)
```

## Technical

Returns a space-separated list of the channels EPIC currently tracks as joined on the selected server. Pending channels for which a JOIN request has been sent but no join confirmation received are not included. Channels already joined but still synchronizing their member or mode information are included.

With no input, the selected server is the current execution server (`from_server`). In a hook or script, this can differ from the current window's server. Supply a server or window explicitly when the distinction matters.

If the input starts with a digit, the first integer function argument selects a server refnum; later arguments are ignored. Conversion detects numeric bases, including hexadecimal and leading-zero octal. Otherwise, the entire input is a window description, optionally prefixed with `#`, and that window's server is selected. Window names and UUIDs are matched case-insensitively. `#0` selects the current window's server. A digit-leading window name needs the `#` prefix to avoid being treated as a server number.

The window description is not separately dequoted; do not add double quotes merely to group a name. A negative number without `#` follows the window-description branch rather than selecting a negative server sentinel.

The selected server must be registered, meaning in EPIC's syncing or active connection state. Invalid server or window descriptions, a disconnected window, an unregistered server, or no tracked channels returns the empty string. No IRC request is sent.

## Practical

Use this when you need all your joined channels on a server, for example to iterate over them in a script. Selecting a window chooses **all channels on its server**, not just channels attached to that window. Both visible and hidden windows can identify a server.

[$currchans()](currchans.md) reports current channels associated with windows. [$channel()](channel.md) instead lists cached nicknames and privilege annotations for one channel; it does not share this function's argument syntax.

## Returns

Joined channel names separated by spaces, without added quoting or status prefixes. Names are returned in the internal channel-list order, not sorted by this function. Do not rely on that order for presentation. The function does not change channel or window state.

## Examples

```text
$mychannels()          channels on the current execution server
$mychannels(0)         channels on server refnum 0
$mychannels(#0)        channels on the current window's server
$mychannels(#2)        channels on window 2's server
$mychannels(chat)      channels on the server of the window named chat
```

A result might be `#example #help`; the actual channels and order depend on local state.

## History

This function first appeared in ircII 2.2.2.

Registration and `function_channels()`: [source/functions.c](../../source/functions.c). Channel-list traversal and join tracking: [source/names.c](../../source/names.c) and [source/parse.c](../../source/parse.c). Window descriptions: [source/window.c](../../source/window.c). Registration-state test: [source/server.c](../../source/server.c).
