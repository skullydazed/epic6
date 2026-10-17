# on disconnect

## Synopsis

```text
/on [<noise>]disconnect "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for several disconnection-related notifications: an operator-style [kill](../commands/kill.md) directed at you, an attempted send through a known server entry whose socket is closed, or a [disconnect](../commands/disconnect.md) command when EPIC's connected-server count is zero.

This is not a universal server-connection closure event. Ordinary socket failure and EOF handling do not invoke this hook. See [on server_lost](server_lost.md), [on server_status](server_status.md), and [on reconnect_required](reconnect_required.md) for related connection lifecycle events.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the full notification text, not a fixed server-name field. There are no event-specific options.

## Parameters and default output

`$*` contains one of these strings:

| Event text | Trigger | Default output |
| --- | --- | --- |
| `Killed by <source> (<reason>)` | An incoming `kill` targets your nickname and the source contains no dot. | `You have been killed by that fascist [<source>] <reason>` |
| `No Connection to <refnum>` | A send reaches a valid server entry with no open socket. | `You are not connected to a server, use /SERVER to connect.` |
| `Disconnected by user request` | The disconnect command reaches its final notification branch while the connected-server count is zero. | The same not-connected message. |

Output includes EPIC's usual message prefix. The first words in `$0`, `$1`, and so on are parts of the text; `$0` is not a server name. The server refnum appears only in the `No Connection to` form. A send with no valid server entry returns before reaching this hook, so not every send from a disconnected context triggers it.

For a kill without a reason, EPIC supplies `No Reason Given`. A kill source containing a dot takes the separate server-rejection path and prints its rejection message without this hook. A kill targeting another nickname is handled through the quit path instead.

The user-request notification is controlled by the connected-server count, rather than being emitted for every individual server close. The command can also reach it when already disconnected. Its normal reconnect branch does not emit that final notification.

## Suppression and effects

A suppressing hook, such as a serial-zero `^disconnect` hook, prevents the corresponding default line. It does not undo a disconnect, cancel a server kill, reconnect a socket, or suppress separate connection messages. The kill notification itself does not close the socket; normal connection termination is processed separately. The hook has no automatic outgoing IRC response.

## Example

Customize the notification using its full text:

```text
on ^disconnect * (message) {
    xecho -b Disconnection notice at $Z: $message;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_kill` distinguishes your kill, server rejection, and other nicknames' kills.
- [source/server.c](../../source/server.c): `vsend_to_aserver_with_payload` emits the closed-socket notification; `disconnectcmd` emits the user-request notification; socket-read failure takes a separate lifecycle path.
- [source/window.c](../../source/window.c): `window_check_servers` maintains the connected-server count.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard matching and suppression.
