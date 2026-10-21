# DCC: removed subsystem

DCC means **Direct Client-to-Client** connection. The former `/dcc [operation [arguments]]` interface managed direct chats, file transfers, and other socket connections, with offers commonly exchanged using CTCP DCC messages over IRC. With no operation it listed connections.

EPIC6 has removed that built-in subsystem. `/dcc`, `dccctl()`, and the associated transfer/chat operations do not have current built-in implementations. No bundled replacement for `/dcc` is supplied. Historical commands, settings, hooks, or scripts that depend on those connections do not acquire working DCC support simply because their names remain in old script text.

## CTCP is not a transfer implementation

The general [CTCP](../commands/ctcp.md) facility remains separate. Sending or observing text named DCC does not create a listening socket, accept an offered connection, transfer a file, or start a direct chat in the current client. The former ignore and flood-control assumptions are also not a current DCC policy interface.

Firewall/NAT instructions for the former subsystem do not enable it in EPIC6. If a separate application or custom implementation provides direct transfers, its own protocol and network requirements apply; this page does not promise the historical initiator/recipient behavior for it.

## Current alternatives

For ordinary messages through IRC, use [msg](../commands/msg.md) or [query](../commands/query.md). For running a separate local program, see [exec](../commands/exec.md); that is not a DCC-compatible replacement by itself.

Repository notes leave open a possible future return of DCC or direct sockets. That is development direction, not an available interface or a promised timetable.

## Source references

- `source/commands.c` and `source/functions.c`: current registries omit the former DCC interfaces.
- `source/ctcp.c`: current CTCP handling without the former DCC subsystem.
- `UPDATES` and `KNOWNBUGS`: removal and possible future redesign.
