# on server_lost

## Synopsis

```text
/on [<noise>]server_lost "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered during the ordinary hard-close path for a server connection whose descriptor is still recorded as open. This includes user-requested disconnections and cleanup after connection failures. It is not guaranteed for every attempted closure: an already-closed descriptor returns before the hook, and a soft reset takes a separate registration path without this event.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches server refnum, configured host, and final message together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Server refnum. |
| `$1` | Configured server hostname or address. |
| `$2-` | Final locally selected quit text when closing a registered connection; otherwise empty. |

The host is not necessarily the server's reported name or resolved peer IP address. The final message is not a reliable network-error diagnosis. When the close path began with a registered server, EPIC uses the explicit close message, or the server's configured quit message, falling back to `Leaving`, and applies quit-message version formatting. If the server was marked unregistered before closure, the hook instead receives empty reason text.

## Ordering and default behavior

The close routine first changes state to `closing`, clears pending waits and channel state, and frees accepted and pending nickname information. Thus channel records and nickname state are already gone when `server_lost` runs; channel cleanup can generate [on channel_lost](channel_lost.md) earlier.

For a connection that was registered when this close routine began, it attempts to send a nonempty quit message, then marks the server unregistered with state `eof` before invoking this hook. Otherwise the close path normally still has state `closing`. After the hook, EPIC closes the descriptor and sets state `closed`. Do not assume the hook always runs in `closing` state.

Although the descriptor is closed afterward in this routine, an underlying connection can already have failed. Its continued usability during the hook is not guaranteed. For observing the earlier transition, use [on server_status](server_status.md) or [on server_state](server_state.md) and match the transition to `closing`.

There is no built-in display attached to `server_lost`, and the caller ignores its default-handling return value. Suppression does not cancel closure, preserve channel state, or prevent the transition to `closed`. The event does not automatically reconnect; other lifecycle logic or scripts can decide what happens next.

## Example

```text
on -server_lost "*" (server, host, message) {
    echo Closing server $server ($host) with quit text: $message;
};
```

An empty message in this notification should not be interpreted as proof that no connection error occurred.

## Source references

- [source/server.c](../../source/server.c): `server_close_internal` controls cleanup, message selection, hook timing, and descriptor closure; `server_is_unregistered` changes state to `eof`; `set_server_state` invokes state and status events.
- [source/names.c](../../source/names.c): `destroy_server_channels` invokes channel-loss hooks before deleting records.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching.
