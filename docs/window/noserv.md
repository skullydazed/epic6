# window noserv

## Synopsis

```text
/window [<window>] noserv
```

## Description

Completely disassociate the selected window from its IRC server. With no window selector, use the current window. The window's server association becomes `noserv`; it does not automatically choose another server. Use [window server](server.md) to associate it with a server again.

[window discon](discon.md) is the same built-in operation. Despite that spelling, this handler does not directly close the server connection or send a request to disconnect. Other windows can continue using the server. If no windows remain associated with it, normal server checking can close the connection when its `autoclose` property is enabled, as it is by default.

Before detaching, EPIC attempts to reassign the window's channels to other windows on their server, preferring windows without both `fixed` and `skip` on. It clears pending channel associations, changes the server value, invokes the `window_server` hook with the window number and old/new server numbers, and requests status updates. If no replacement channel window exists, there is no immediate destination from that reassignment helper; normal channel consistency checks still apply.

The window retains its stored output, settings, and query/nick target-list entries. It no longer matches ordinary server-specific channel or nick routing for the old server. Explicit output, such as [xecho](../commands/xecho.md) with `-w`, can still reach it. Output without a server and final routing fallbacks can also reach a detached window, so detachment does not guarantee silence.

## Arguments and effects

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations on the same selected window. `/window -noserv` is a no-op. Detachment does not change visibility, geometry, or input selection and works on invisible windows. Repeating it still runs the handler and server-change hook, even if the association is already `noserv`.

There is no ordinary success message. Hooks, channel reassignment, or later server housekeeping can produce output.

## Examples

```text
/window noserv
/window 3 noserv
/window 3 describe
```

Detach the current window or window 3, then inspect window 3's association. To associate it with configured server 0 again:

```text
/window 3 server 0
```

## History

This feature first appeared in EPIC4pre1.028.

## Source references

- [source/window.c](../../source/window.c): the command table maps `noserv` to `windowcmd_discon`; that handler, `destroy_window_waiting_channels`, and `window_change_server` implement detachment and notifications. `window_check_servers` handles possible autoclose.
- [source/names.c](../../source/names.c): `reassign_window_channels` attempts channel reassignment.
- [source/screen.c](../../source/screen.c): `add_to_screen` implements explicit destinations, server-aware routing, and fallbacks.
