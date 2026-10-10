# reconnect

Close the current window's server connection and start another connection attempt.

## Synopsis

```text
/reconnect
```

## Description

`reconnect` manages a server's connection state while keeping EPIC running. It uses the server associated with the current window, closes its connection, then tries the next address from any remaining cached DNS results. If that attempt cannot start, it sets the server to the reconnect state; a window associated with that server then initiates a fresh DNS lookup and connection sequence.

The command works on an already connected server as well as an unfinished or closed connection. It has no dependable built-in safe mode that refuses to reconnect an active session. It normally prints disconnection and reconnection notices. A registered open connection receives an attempted IRC quit message with the reason `Reconnecting` before it closes; the server determines how the departure is reported. A successful nonblocking connection start does not guarantee eventual connection or registration.

Closing clears tracked channels, their window affiliations, pending channel assignments, and confirmed/pending nickname state. The server record, configured connection information, and window/server association remain. This built-in does not preserve and rejoin your former channels itself.

## Arguments and current limitations

The historical syntax is `reconnect [server [reason]]`, but the current shared parser does not implement it correctly. Tokens without a leading dash, including ordinary server numbers, names, and reason words, are consumed without selecting a server or supplying a reason. Thus `/reconnect 2 I'll be back` can reconnect the current window's server with the default reason instead of server 2.

Dash-prefixed tokens enter server-description lookup and normally report `No such server!`; the intended `-force` and `-safe` flags are not dependable options. Use the no-argument form after selecting a window associated with the intended server. See [window](window.md) and [server](server.md).

If the current window has no valid server while another connection exists, the command reports an unknown-server error. With no connections and no valid selected server, the `disconnect` [on](on.md) hook can suppress the normal not-connected message. The command is always registered, returns no value, and does not wait for reconnection to finish.

## Related behavior and example

[disconnect](disconnect.md) uses the same close operation but does not request another connection attempt. The bundled [reconnect script](../../script/reconnect) implements separate automatic reconnection and channel-rejoining policy after connection losses; loading or configuring that script is separate from running this built-in.

To restart the connection belonging to your current window:

```text
/reconnect
```

## History and source

`reconnect` first appeared in `plus-3`, after ircII and before EPIC.

Registration is in [source/commands.c](../../source/commands.c). `reconnectcmd`, the shared `disconnectcmd` parser, closing, cached-address attempts, and DNS bootstrapping are in [source/server.c](../../source/server.c). Window/server association and reconnect-state processing are in [source/window.c](../../source/window.c).
