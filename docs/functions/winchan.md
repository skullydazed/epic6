# winchan

Look up a window's current channel, or the window that owns a channel.

## Synopsis

```irc
$winchan()
$winchan(<window refnum, name, or UUID>)
$winchan(<channel> [server description])
```

## Technical

[chanwin](chanwin.md) and `winchan` are the same function, with no command-name differences.

With no argument, the result is the current window's current channel. With a window reference, name, or UUID, it returns that window's current channel. Reference `0` selects the current window. Name and UUID lookup is case-insensitive and includes hidden windows. An unknown window or a window without a current channel returns an empty string; later arguments in this form are ignored.

If the first argument is recognized as a channel name, the function instead returns the owning window's user reference number. This works for any tracked channel, including one that is not its window's current channel. Channel recognition uses the current execution server's `CHANTYPES` or the client's fallback prefixes, even when a different server is supplied for the lookup.

The channel form defaults to the current execution server. The entire remainder after the channel argument, if present, is resolved as a server description against existing records; a server reference number or matching server name can be used. No record is added and no connection is made. A server value of `NOSERV` (`-2`), including a failed description lookup, falls back to the primary server during channel lookup. The function returns `-1` if the channel or its owning window is not found.

The first argument uses normal function word parsing controlled by `/xdebug dword`. `*` is not ordinarily a special current-channel argument: unless recognized as a channel prefix, it is treated as a window description.

## Practical and returns

Use the channel form to find where a joined channel is attached, and the window form to obtain its currently selected channel. These are local queries and do not change either selection. In an [on](../commands/on.md) handler, pass the event's channel explicitly when that is what you need; a no-argument call uses the current window.

The return is a channel string or empty string for window lookup, and a numeric window reference or `-1` for channel lookup.

## Examples

If window 2 currently selects and owns `#epic` on the execution server:

```irc
$winchan(2)       /* #epic */
$winchan(#epic)   /* 2 */
$winchan(#epic 0) /* Look up the channel on server 0. */
$winchan(0)       /* Current window's current channel. */
```

## History

`winchan` first appeared in EPIC4pre0.034. It was swapped with `chanwin` in EPIC4pre1.027; the functions were unified in EPIC4pre1.041. Support for non-current channels appeared in EPIC4pre2.506.

## Source

Registered and implemented as `function_winchan` in [source/functions.c](../../source/functions.c). Channel ownership and primary-server fallback are in [source/names.c](../../source/names.c); window descriptions are resolved in [source/window.c](../../source/window.c), server descriptions in [source/server.c](../../source/server.c), and channel prefixes in [source/parse.c](../../source/parse.c).
