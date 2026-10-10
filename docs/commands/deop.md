# deop command

## Synopsis

```text
/deop
```

## Description

`deop` asks the current IRC server to remove your IRC-operator status. It reads your nickname from EPIC's current server record and sends `MODE your-nickname -o`.

This removes a user mode, not channel-operator privileges. It does not accept another person's nickname or a channel as a target. There are no options or required arguments, and any supplied text is ignored.

The command does not check whether you currently have operator status and does not change local mode state immediately. The server determines whether the request is accepted and which privileges are removed. Replies and mode changes arrive asynchronously; EPIC updates its tracked user modes when it processes server mode messages. Normal mode reporting can be customized with [`on`](on.md) hooks.

The command is available in every build and requires a usable IRC connection to send the request. It performs no registration check of its own, so use it after your nickname has been registered. A closed connection normally produces the sending layer's not-connected message, which a `disconnect` hook can suppress.

## Example

To relinquish your own IRC-operator status on the current server:

```text
/deop
```

## Related commands

This has the same requested user-mode effect as `/mode your-nickname -o`. See [`mode`](mode.md) for user and channel modes. To remove channel-operator privileges, use the appropriate channel-mode operation instead; this command does not perform it.

## Source

See [source/commands.c](../../source/commands.c) for the `deop` registration and handler, [source/server.c](../../source/server.c) for nickname lookup and sending, and [source/parse.c](../../source/parse.c) for server MODE processing and user-mode updates.
