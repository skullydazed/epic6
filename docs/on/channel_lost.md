# on channel_lost

## Synopsis

```text
/on [<noise>]channel_lost "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC removes a channel membership record. Common causes are your own [part](../commands/part.md), being [kicked](kick.md), [disconnecting](../commands/disconnect.md), or losing the server connection. The event follows the client's processing of the server response or connection closure, rather than simply sending a part request.

It also occurs when EPIC discards an existing channel record in response to certain server errors or while repairing channels whose server has no usable windows. It therefore reports loss of the client's channel state, without guaranteeing a particular server-side cause. Removing an unknown channel does not generate this event.

Use the normal [on](../commands/on.md) pattern and noise controls. The pattern matches all three fields below together; there are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Server refnum. |
| `$1` | Channel name. |
| `$2` | User-visible refnum of the window associated with the channel, rather than its internal window ID. |

No reason is supplied in `$3-`. Handlers can ignore extra trailing fields for forward compatibility, but should not expect a part, kick, or disconnect reason here. Use the corresponding event when that information is needed.

The hook runs before the record is destroyed. During server disconnection it runs separately for each retained channel on that server. A channel may still be discoverable by queries during the handler, even though it is about to be removed. Do not treat this as an opportunity to veto removal.

## Default behavior and limitations

This event has no built-in output. Suppressing its default action does not prevent channel removal or suppress the separate part, kick, or disconnection messages. Handling it does not automatically rejoin the channel or send any IRC request.

This hook covers normal individual removal and per-server cleanup. The final bulk channel cleanup during client shutdown bypasses it, so it is not a guaranteed notification for every record discarded while EPIC exits.

## Example

```text
on ^channel_lost * (server, channel, window) {
    echo Lost $channel on server $server (window $window);
};
```

## History

This event first appeared in EPIC5-0.3.2. The window refnum in `$2` first appeared in EPIC5-2.1.6.

## Source references

- [source/names.c](../../source/names.c): `remove_channel` and `destroy_server_channels` invoke the hook before destruction; `window_check_channels` can trigger server-channel cleanup.
- [source/parse.c](../../source/parse.c): `p_part` and `p_kick` remove your channel membership after processing the corresponding events.
- [source/server.c](../../source/server.c): `server_close_internal` destroys channels when closing a server connection.
- [source/numbers.c](../../source/numbers.c): numeric processing can remove channel records in response to server errors.
- [source/irc.c](../../source/irc.c): final cleanup uses bulk `remove_channel(NULL, 0)` without this hook.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard hook handling.
