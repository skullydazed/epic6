# whowas command

## Synopsis

```text
/whowas [nickname [count [server]]]
```

## Description

`whowas` asks an IRC server for retained information about past uses of a nickname, such as a user who disconnected or changed nicknames. It is related to [`whois`](whois.md), but historical data is usually more limited: common replies contain the nickname, username, hostname, realname, and associated server, rather than current channels, away state, or idle information.

The server determines how many records it retains and for how long. Records may already have expired or be unavailable, and a nickname can have been used by several different people. Historical data is not proof of a user's current identity or presence.

A second argument conventionally requests a count, and a third selects a remote server where supported. EPIC forwards these arguments without validating or inserting a count; with only a nickname, the server chooses its default count. The C built-in does not consult `num_of_whowas`. The bundled [`builtins`](../../script/builtins) script defines that setting for compatibility, but this handler does not use it.

The first word is parsed as the nickname and joined to the remaining argument text. Quotes are not removed, additional arguments are forwarded, and there are no client-side options. With no arguments, EPIC uses your current server nickname. The outgoing line uses the server's encoding. You can supply a remote server directly to this command; using [`quote`](quote.md) is unnecessary.

## Output and restrictions

Replies arrive asynchronously. Numeric 314 is displayed as `nickname was username@hostname (realname)`. Numeric 312 supplies server information, and 369 conventionally ends the response. Other server-supplied fields and errors use normal numeric handling. Numeric-specific [`on`](on.md) hooks and the general numeric hook can handle or suppress the output. The built-in has no callback option or function return value.

An IRC connection is required to send. Count limits, target-server support, privacy restrictions, retained fields, and error behavior depend on the network. Lack of historical records does not establish that a nickname was never used. The command is always registered.

## Examples

Request up to five retained uses of JoeBob's nickname, if the server supports that count:

```text
/whowas joebob 5
```

Ask a particular server for the same information where remote queries are supported:

```text
/whowas joebob 5 irc.example.net
```

## Source

[source/commands.c](../../source/commands.c) registers `whowas` and implements its command-name branch in `whois()`. [source/server.c](../../source/server.c) sends the request. [source/numbers.c](../../source/numbers.c) implements numeric hooks and 314/312/369 formatting.
