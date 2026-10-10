# disconnect command

## Synopsis

```text
/disconnect
```

## Description

`disconnect` closes the IRC connection associated with the current window without quitting EPIC. Other server connections are left alone. It normally reports `Disconnecting from server ...` and refreshes the status display.

For a registered connection, EPIC sends a QUIT message with the default reason `Disconnecting` before closing the connection. The server controls how that departure is reported to others. The command does not wait for acknowledgement.

Joined channels and their window affiliations on that server are removed, along with pending channel assignments. The server record itself remains, so this is not deletion from the server list. Stored configuration and the requested away message are not removed by this close operation.

## Arguments and current limitations

The historical interface is `disconnect [server-number [reason]]`, but the current argument parser does not implement that form correctly. Tokens not beginning with `-`, including a numeric server reference and ordinary reason words, are consumed without selecting a server or supplying a reason. Consequently, `/disconnect 2 I'll be back later!` can disconnect the current window's server with the default reason instead of server 2.

Dash-prefixed arguments enter server lookup and normally report `No such server!` rather than acting as the intended `-force` or `-safe` options. These flags are therefore not dependable options in the current implementation. Use the no-argument form in a window associated with the server you intend to disconnect.

The command is available in every build. If there is no valid current-window server and another connection exists, it reports an unknown-server error. When the client has no connections, a `disconnect` hook can handle or suppress the normal not-connected message. See [`on`](on.md).

## Technical

Closing an open connection passes through `closing` to `closed`. `disconnect` does not request a new connection or attempt another DNS address afterward. [`reconnect`](reconnect.md) uses the same front-end handler but performs an additional connection attempt; it is the command intended to try another available address while connecting.

If the server record has no open descriptor, the close helper still clears channel state and sets `closing`, but returns before setting `closed`. Thus the exact final state differs for an already closed or unfinished connection.

## Example

To disconnect from the server of your current window:

```text
/disconnect
```

Select a window on another connected server before using this command to disconnect that server. See [`window`](window.md) and [`server`](server.md).

## History

The `disconnect` command first appeared in “plus 3” (post-ircII, pre-EPIC).

## Source

See [source/server.c](../../source/server.c) for `disconnectcmd()`, its command-name distinction from `reconnect`, argument parsing, `server_close()`, and `server_close_internal()`. [source/commands.c](../../source/commands.c) registers the command; [source/window.c](../../source/window.c) supplies the current window's server and pending-channel cleanup.
