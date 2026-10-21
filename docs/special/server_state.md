# Server states

Each entry in the [server list](server_list.md) has a state describing its connection lifecycle. Being present in the list does not mean a connection is open or IRC registration has succeeded.

## Valid states

These are the strings returned by the client:

| State | Meaning |
| --- | --- |
| `CREATED` | Entry is being added to the server list. |
| `RECONNECT` | Ready to start a connection when a window is associated with it. |
| `POLICY` | Connection bootstrap gives scripts an opportunity to adjust the server description before DNS. |
| `DNS` | Asynchronous address lookup is in progress. |
| `CONNECTING` | A nonblocking network connection is being attempted. |
| `SSL_CONNECTING` | TLS negotiation is in progress. |
| `REGISTERING` | IRC registration is in progress. |
| `SYNCING` | Registration has succeeded; initial client setup is running. |
| `ACTIVE` | Initial client setup has finished. |
| `EOF` | Connection-loss/unregistration state. |
| `ERROR` | A connection error requires cleanup. |
| `CLOSING` | Connection and channel state are being cleaned up. |
| `CLOSED` | Connection has been closed. Saved addresses may still allow another attempt. |
| `DELETED` | Entry is being removed from the server list. |

`active` does not promise that every server operation is permitted, authentication services have finished, channels have been joined, or all pending replies have arrived. Server policy and individual operations still determine those results. `eof` and `error` are not reliable indications of which party was at fault, and not every close follows one fixed sequence through all these states.

## Connecting to a server

A newly added entry is placed in `reconnect`. The window/server check starts it when an associated window exists: `policy`, then `dns`, then an attempt to connect to an address. TLS connections also pass through `ssl_connecting` before IRC registration.

One hostname lookup can supply several addresses. Before registration succeeds, a failed attempt can leave the entry `closed` with addresses still untried. With an associated window, the client then tries the next address. Thus `closed` alone does not mean the overall connection attempt is finished.

On receipt of the registration welcome, EPIC discards the saved DNS results and enters `syncing`. It restores initial settings, runs [on connect](../on/connect.md), and then enters `active`. These steps do not wait for every asynchronous setup request to receive a reply.

The operations are asynchronous, but they are not an uncancellable transaction. [disconnect](../commands/disconnect.md) closes the current connection; before successful registration, remaining saved addresses can still cause another attempt. [reconnect](../commands/reconnect.md) can immediately try a remaining address, or restart the lookup when none can be tried.

## Observing a connection attempt

[on server_state](../on/server_state.md) and [on server_status](../on/server_status.md) both receive the server refnum, old state, and new state, in that order. The state is already changed when the hooks run; their noise mode does not veto the transition. The state hook runs before the status hook.

```irc
on server_state "% % SYNCING" {
    xecho -b Server $0 has been accepted onto IRC.;
};
on server_state "% % CLOSED" {
    if (serverctl(GET $0 ADDRSLEFT) == 0) {
        xecho -b Server $0 is closed with no remaining addresses.;
    };
};
```

The second message is an observation, not proof that an initial connection failed: an established connection closing normally also has no saved addresses. Scripts can initiate another connection from a hook. Do not equate a snapshot with a permanent terminal condition.

Use `$serverctl(get <refnum> state)` to query the current state; `status` is a compatibility synonym. `$serverctl(get <refnum> addrsleft)` reports the number of saved addresses not yet tried. See [serverctl](../functions/serverctl.md).

## Disconnecting and reconnecting

Closing a connection cleans up its channel state and normally ends in `closed`. The core does not continually reconnect an established session after loss. Once the saved addresses are exhausted or discarded, a new connection must be requested. There is also a limited core fallback after a connection closes without receiving data: it can change between plain IRC and TLS according to the configured port and restart the connection.

To request another connection for the current window's server, use `/reconnect`. To associate a window with an entry and request its connection, use `/window server <refnum>` or the appropriate [server](../commands/server.md) command. These are requests, not guarantees that DNS, TLS, or IRC registration will succeed.

For automatic recovery after a lost established connection, load the [reconnect script](../scripts/reconnect.md). Its timers, retry policy, and channel restoration are script-provided behavior. Initial connection failures do not by themselves trigger that script's loss event.

## History and source

Server states originated in EPIC5's nonblocking connection work and expanded with asynchronous DNS, TLS, and scripted reconnection.

- `source/server.c`: state names, transitions, bootstrap, address iteration, registration, close paths, and `serverctl` queries.
- `source/window.c`: connection bootstrap and retries for servers with associated windows.
- `source/numbers.c`: registration welcome handling.
- `script/reconnect`: optional automatic recovery policy.
