# chankey function

## Synopsis

```text
$chankey(server-refnum channel)
```

## Technical

`chankey()` returns EPIC's cached mode `+k` key for a channel on the selected server. Both arguments are required; it does not default to the current server or channel when an argument is omitted. The first argument is an integer server reference number, not a server name or description. Further words after the channel argument are ignored.

Server-number conversion uses `strtoimax()` with base detection, including hexadecimal and leading-zero octal notation. Nonnumeric input converts to zero, and a numeric prefix can be accepted with trailing nonnumeric text. The special server value `-2` selects the primary server through the channel lookup helper. Channel names are matched using that server's case mapping.

An explicit channel argument of `*` selects the current window's current channel name, then looks up that name on the requested server. It does not select a different window or use that server's own current channel. Channel argument parsing honors [`xdebug dword`](../commands/xdebug.md).

## Practical

This is a local cache query: it sends no IRC request and does not discover the key of an arbitrary channel. The channel must have a record in EPIC's tracked channel list. The stored key is updated from channel-mode information the client processes, including received mode changes and initial channel synchronization.

The result is whatever key argument EPIC has stored. Server policy can conceal a key or replace it with a placeholder; this function cannot recover a secret the server has not supplied. Missing mode arguments or incomplete synchronization can also leave the cached value unavailable or stale. Use [`mode`](../commands/mode.md) to request server information when appropriate, observing that replies are asynchronous and server-dependent.

## Returns

The stored key as a string, or the empty string if an argument is missing, no matching channel record exists, or no key is stored. An empty result does not distinguish an unkeyed channel from unavailable information.

## Examples

```text
$chankey(0 #example)
$chankey(0 *)
```

The first returns the cached key for `#example` on server 0. The second uses the current window's channel name on server 0. Either returns empty when no key is available.

## Source

[source/functions.c](../../source/functions.c) registers `chankey` and implements `function_chankey()`. [source/names.c](../../source/names.c) implements `get_channel_key()`, channel lookup, and mode-key updates. [source/parse.c](../../source/parse.c) and [source/numbers.c](../../source/numbers.c) supply received mode information. [include/server.h](../../include/server.h) defines the primary-server lookup sentinel, and [include/functions.h](../../include/functions.h) defines argument conversion.
