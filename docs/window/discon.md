# window discon

## Synopsis

```text
/window [<window>] discon
```

## Description

Set the selected window's server association to `noserv`, meaning it is no longer associated with an IRC server. With no selector, operate on the current window. This is the same built-in operation as [window noserv](noserv.md).

Before detaching, EPIC attempts to reassign the window's channels to other windows on their server and clears its pending channel-association list. It changes the window's server value, raises the `window_server` [on](../commands/on.md) event with the window number and old/new server numbers, and requests status updates.

The window retains its stored output, local settings, and target-list entries. It no longer receives ordinary server-associated channel or nickname routing for the old server. Explicit output through [xecho](../commands/xecho.md) can still reach it, as can other output explicitly directed to that window.

The window does not automatically choose another server simply because it is detached. Use [window server](server.md) to associate it with a server again.

## Connection effects

Detaching a window is not the same operation as [disconnect](../commands/disconnect.md). The handler does not directly send an IRC request or close the old server connection, and other windows can continue using it. However, if no windows remain associated with that server, normal server housekeeping can close it when that server's autoclose behavior is enabled. Channel state also undergoes normal window-association consistency checks.

## Arguments and examples

There are no operation-specific arguments or options. Additional words are processed as subsequent window operations. `/window -discon` is a no-op. The operation has no ordinary success message of its own; hooks, channel reassignment, or subsequent housekeeping can produce output.

```text
/window discon
/window 3 discon
```

Detach the current window, or window 3, from its server. To inspect the resulting association:

```text
/window 3 describe
```

## Source references

- [source/window.c](../../source/window.c): `windowcmd_discon`, `destroy_window_waiting_channels`, `window_change_server`, and `window_check_servers` implement detachment, the hook, pending-list cleanup, and possible server autoclose.
- [source/names.c](../../source/names.c): `reassign_window_channels` attempts to move channel associations to other windows.
- [source/screen.c](../../source/screen.c): output routing uses server and channel/target associations.
