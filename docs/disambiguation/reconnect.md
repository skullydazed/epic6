# Reconnect

Do you mean:

- The built-in [reconnect command](../commands/reconnect.md), `/reconnect`, which closes the current window's server connection and requests another connection attempt?
- The [reconnect script](../scripts/reconnect.md), loaded with `/load reconnect`, which schedules automatic recovery after a lost registered connection and attempts to restore remembered channels?

The built-in tries a remaining cached address or resets the connection to start a new lookup. It does not itself restore former channels or provide a continuous retry policy. Use the no-argument form after selecting a window on the intended server; the command page documents limitations in the historical server/flag argument parser.

The script supplies timers, settings, retry policy, and channel bookkeeping. Initial connection failure does not by itself generate the loss event that starts its recovery cycle. Neither interface guarantees successful DNS, TLS, IRC registration, authentication, or channel admission.

See [server states](../special/server_state.md) for connection stages and address retries, and [disconnect](../commands/disconnect.md) for closing without an explicit reconnect request.

## Source references

- `source/server.c`: shared disconnect/reconnect handler, address retries, and loss-event delivery.
- `source/window.c`: window-associated connection bootstrap.
- `script/reconnect`: automatic recovery policy and channel restoration.
