# userip command

## Synopsis

```text
/userip [-direct] [-count number] [-extra variable] [nickname ...] [-cmd { commands }]
/userip -flush
```

## Description

`userip` uses the same request and callback machinery as [`userhost`](userhost.md), but sends `USERIP` instead of `USERHOST`. On a supporting server, this asks for an IP address rather than a hostname. The server determines which addresses it exposes, whether they are IPv4 or IPv6, and which privileges are required; a dotted-quad address is not guaranteed.

With no nicknames and no callback, it queries your own nickname. Ordinary output always requests server data and displays returned entries in the same `nickname is username@address` format as `userhost`, including operator and away annotations. Missing nicknames produce no ordinary display. There is no function return value.

A `-cmd` callback can run synchronously if every nickname is already in EPIC's **userhost cache**. That cache is not a separate IP-address cache: the address field may be a hostname, and operator and away flags are forced to non-operator/not-away. Use `-direct` to request fresh IP information from the server. If any nickname is uncached, the entire list is queried from the server and callbacks run later.

## Options and callbacks

| Option | Effect |
| --- | --- |
| `-direct` | Bypass the userhost cache and send a server request. |
| `-count number` | Set nicknames per request; default five. Use a positive count within the server's supported limit. |
| `-extra variable` | Capture the named variable's current value for later callback or output-hook arguments. |
| `-cmd commands` | Treat all remaining text as the callback body, optionally braced. Put nicknames and other options before it. |
| `-flush` | Discard requests waiting to be sent in the shared per-server userhost queue; outstanding requests remain. |

Callbacks run once for each requested nickname in request order, with their own local scope and argument context. `$0` is the nickname, `$1` is `+` for an operator or `-` otherwise, `$2` is `+` for away or `-` otherwise, `$3` is the username, `$4` is the returned address, and `$5-` is extra data. A missing nickname receives `<UNKNOWN>` for username and address and `-` for both flags. Callback handling replaces normal output and its per-entry numeric hook.

The inherited options are case-insensitive; `-d`, `-co`, `-e`, `-c`, and `-f` abbreviate the table's options, respectively. No count validation is performed. With `-cmd` but no preceding nicknames, the command reports `USERIP -cmd with no nicks specified`. See `userhost` for the remaining argument parsing, batching, queue behavior, and capture details.

## Restrictions and replies

The built-in is always registered but silently does nothing unless the current server is registered. Server support is an independent requirement; an unknown-command or privilege error does not supply the queued address reply.

EPIC routes numeric 302 through the shared reply queue. Numeric 340 is routed there only if the server advertises `USERIP` in its 005 capabilities; otherwise it receives ordinary numeric handling and does not finish the queued request. For normal queued output, the corresponding numeric hook receives nickname, operator sign, availability sign, username, address, and extra data. Its availability sign is `-` for away and `+` for available, the opposite of the callback's away sign.

Requests normally have one batch outstanding at a time. Missing, unsupported, or incompatible replies can leave later requests waiting. Use this command rather than raw [`quote`](quote.md) requests, which can interfere with the shared queue. [`usrip`](usrip.md) sends a different legacy wire verb; it is an alternative only when your server documents that form, not a guaranteed fallback.

## Example

On a server supporting the request and reply format:

```text
userip -direct joebob jimbo -cmd {
    echo $0 has server-reported address $4
}
```

`-direct` avoids substituting a cached hostname for the requested address. The server may still withhold or mask an address.

## Source

[source/who.c](../../source/who.c) implements `useripcmd()`, the `USERIP` branch of `userhostbase()`, and shared queues and callbacks. [source/numbers.c](../../source/numbers.c) handles numerics 302 and 340, including the 005 capability check. [source/commands.c](../../source/commands.c) registers the command.
