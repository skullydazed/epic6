# on send_to_server

## Synopsis

```text
/on [<noise>]send_to_server "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered in the formatted server-send path immediately before EPIC writes the resulting IRC line. It can observe or suppress lines destined for a server, including commands generated internally by the client. This differs from message-specific send hooks, which normally control local echo rather than delivery.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches server refnum, file descriptor, and outgoing text together. There are no event-specific options. Prefer a narrower event when it provides the required information, since this hook participates in ordinary outgoing protocol traffic.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Target server refnum. |
| `$1` | Connection's operating-system file descriptor. |
| `$2-` | Prepared IRC line, including its appended carriage return and newline. |

The line is already encoded for the destination, not necessarily UTF-8. Outbound mangling and recoding, payload attachment, and the final IRC buffer-size truncation have occurred before this hook. It is therefore not necessarily identical to the original command text. The descriptor is a connection identifier, not a server refnum or stable identifier across reconnections.

The sender temporarily selects the target server as the hook's server context. Invalid server references or known servers without an open socket return before this event. Callers that use the raw socket-writing helper directly bypass it.

## Default behavior and suppression

There is no built-in local display. If the hook permits default handling, EPIC writes the prepared line using the plain or TLS socket writer as appropriate. This is not a delivery acknowledgment: a write failure or server rejection can still prevent the intended result.

A suppressing hook, such as a serial-zero `^send_to_server` hook, prevents that write. This can cancel protocol commands and disrupt registration, keepalive replies, or state synchronization. Earlier local echoes and caller-side state updates are not rolled back. The sender has also marked the server as having sent data before it invokes this hook.

Changing handler variables does not replace the prepared buffer. To replace a line, a script must suppress the original and explicitly issue its replacement, accounting for encoding and protocol framing rather than blindly reusing these bytes as ordinary command text. The old DCC-handshake example does not apply because EPIC6 removed the built-in DCC transport.

## Recursion

The hook is marked nonrecursive. If a handler sends another command through this path, its nested `send_to_server` handlers are skipped and the nested write can proceed. This avoids immediate hook re-entry but does not make every script-level loop safe.

## Example

Observe ordinary outgoing private-message lines without suppressing them:

```text
on -send_to_server "% % PRIVMSG *" {
    echo Outgoing message on server $0 (fd $1);
};
```

This prints connection metadata rather than replaying or changing the encoded line.

## Source references

- [source/server.c](../../source/server.c): `vsend_to_aserver_with_payload` builds, encodes, mangles, truncates, and terminates the line, selects the server context, and invokes this hook; `send_to_aserver_raw` performs the actual socket or TLS write and handles write errors.
- [source/hook.c](../../source/hook.c): event registration sets the nonrecursion flag; dispatch implements matching and suppression.
