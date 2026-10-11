# whois command

## Synopsis

```text
/whois [nickname[,nickname...]]
/whois server-or-nickname nickname[,nickname...]
```

## Description

`whois` asks your IRC server for detailed information about a user or users. Common replies include nickname, `user@host`, realname, server, visible channels and channel status prefixes, away message, and IRC operator status. Some servers also provide account, connection security, idle time, or sign-on time. The server determines which fields are supplied and which channels or addresses it exposes.

With one argument, the conventional form queries the connected server for a comma-separated nickname list. With two arguments, the first selects a remote server, or a nickname whose server should answer, and the second identifies the users to query. These target forms and multi-nickname support depend on the network. A common request for a user's own-server information is `/whois nickname nickname`, which may obtain idle information unavailable from your local server.

With no arguments, EPIC queries your own nickname. Otherwise it forwards all supplied argument text after `WHOIS`, without splitting nickname lists, resolving a target server locally, or limiting the number of arguments. There are no client-side options, nickname validation, or quote removal. The whole outgoing line uses the server's encoding.

## Output and restrictions

Replies arrive asynchronously as separate numerics. EPIC formats common fields, including 311 user/host/realname, 312 server, 313 operator information, 319 channel list, 301 away message, 330 account, and 338 additional host information. Numeric-specific [`on`](on.md) hooks and the general numeric hook can handle or suppress normal output. Numeric 318 conventionally ends a WHOIS response. There is no built-in callback option or function return value.

For numeric 317, idle intervals above 59 seconds are displayed as whole minutes, discarding the remaining seconds; shorter intervals use seconds. An optional nonzero sign-on timestamp is displayed using the client's time formatting. The server defines what activity counts toward idle time, so it need not mean time since the last message in every implementation.

A connection is required to send the query. Remote requests may be rejected, rate-limited, or disclose fewer fields, and the absence of a reply is not a reliable offline test. Private or secret channel membership and real addresses can be hidden or revealed according to server policy and requester privileges. Use [`whowas`](whowas.md) for retained history rather than current-user information.

## Examples

```text
/whois joebob,toejam,junebug
/whois irc.foo.com joebob,toejam,junebug
/whois joebob joebob
```

These conventionally query several users locally, query them through a specified server, or request JoeBob's own-server information. Server support and visibility rules apply to all three.

## Privacy

Do not assume WHOIS is private: servers can log requests or notify targeted users or operators. The realname field is client-supplied text, not verified identity. EPIC registers its own realname using the per-server default or [`default_realname`](../settings/default_realname.md). At startup the default can come from `IRCNAME`, `NAME`, or `REALNAME`, an existing default setting, the local account's GECOS field, or a username fallback. Inspect the configured value if you do not want account information disclosed. Changing a default affects subsequent registration rather than automatically rewriting an existing connection's realname.

## Source

[source/commands.c](../../source/commands.c) registers `whois` and implements its branch of the shared `whois()` handler. [source/server.c](../../source/server.c) sends requests and selects registration realnames. [source/numbers.c](../../source/numbers.c) implements reply hooks and formatting; [source/irc.c](../../source/irc.c) initializes realname defaults.
