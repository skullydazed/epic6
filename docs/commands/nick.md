# nick

Change or display your nickname on the current server.

## Synopsis

```text
/nick [new-nickname]
```

## Description

`nick` requests a nickname change. If the server rejects the request, for example because someone else already uses that nickname, your established nickname normally remains unchanged. The server decides which nicknames are valid, their maximum length, and whether you may change names at that time. EPIC does not enforce a fixed nickname length or validate the nickname's character syntax here.

With no argument, the command displays your current nickname and the server reference number. It also displays a pending nickname change, if any. Before registration, the current name can appear as `<not registered yet>`. Only the first whitespace-delimited argument is used; additional words are ignored. There are no options.

A request records a pending nickname and the server's preferred nickname for subsequent registration. When the server connection is open, EPIC sends `NICK new-nickname`. Your confirmed nickname changes when the server acknowledges it, rather than immediately when you run the command. A confirmed change updates channel nickname records and status displays; `channel_nick` and `nickname` hooks control the corresponding notification output. See [on](on.md).

If the current context identifies a disconnected server, the request is remembered, but `nick` does not reconnect it. With no server context, the command reports `I can't figure out what server to change your nickname for`. During initial registration, a rejected nickname invokes the `new_nickname` hook and, unless the hook supplies another name, asks you to use `/nick` to continue connecting. If the server has disconnected you, reconnect with [server](server.md) as well.

A confirmed nickname becomes that server's preferred nickname. For the primary server, it also updates EPIC's global default nickname. A server-assigned unique identifier is treated specially: EPIC retains `0` as the server's preferred nickname so a later registration can request another assigned identifier. Scripts can also request a change through `$serverctl(set server-reference nickname new-nickname)`; this uses the same change machinery rather than merely changing a local display value.

## Nickname comparisons

Nicknames are case-insensitive according to the server's comparison rules. Under RFC1459 case mapping, these characters compare equivalently:

```text
ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]
abcdefghijklmnopqrstuvwxyz{|}
```

Thus `[testnick]` and `{testnick}` compare as the same nickname under that mapping. Servers advertising ASCII `CASEMAPPING` do not equate those brackets and braces. EPIC follows the server's advertised mapping for nickname comparisons; accepted nickname characters and lengths remain server policy.

## Examples

Change your nickname to `foobar`:

```text
/nick foobar
```

Display your current nickname and any pending change:

```text
/nick
```

## Source

The always-registered `nick` command and `e_nick` argument handling are in [source/commands.c](../../source/commands.c). Pending names, registration defaults, confirmation, rejection recovery, `serverctl`, and advertised case mapping are in [source/server.c](../../source/server.c). Incoming nickname changes and hooks are in [source/parse.c](../../source/parse.c); nickname error numerics are handled in [source/numbers.c](../../source/numbers.c).
