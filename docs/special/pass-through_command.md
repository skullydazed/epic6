# Pass-through command

A pass-through command forwards its command name and argument text to the current IRC server with little command-specific interpretation. The server defines the accepted arguments, permissions, replies, and errors. Sending the request does not guarantee that the server supports it or that it succeeds.

“Pass-through” does not mean that all client processing is bypassed. Interactive command parsing, script expansion, alias dispatch, outgoing text processing, and send hooks can still apply.

## Syntax and examples

```irc
/time
/names #example
/stats u
```

The generic handler sends the command name alone when no argument text is supplied, or the name followed by a space and the supplied text. It does not add missing protocol parameters or interpret shell-style quotes. Commands requiring a trailing parameter containing spaces need the appropriate IRC colon syntax unless their own documented handler supplies it.

Commands using this generic path include [admin](../commands/admin.md), [connect](../commands/connect.md), [links](../commands/links.md), [list](../commands/list.md), [lusers](../commands/lusers.md), [map](../commands/map.md), [motd](../commands/motd.md), [names](../commands/names.md), [servlist](../commands/servlist.md), [silence](../commands/silence.md), [stats](../commands/stats.md), [time](../commands/time.md), and [trace](../commands/trace.md). Consult each command's page rather than assuming that every server accepts the same forms. The IRC `connect` request is distinct from [server](../commands/server.md), which manages this client's connections.

Other commands can perform substantial local work before sending a request. For example, `part` uses a different shared handler that can supply the current channel and recode a reason separately. A command being server-facing does not by itself make its argument handling identical to the generic pass-through path.

## Client processing and limits

Script statements undergo normal [expando substitution](expando_substitution.md) before dispatch. Ordinary interactive input does not receive that general script pass. A loaded alias of the same name takes precedence; `//command` bypasses an alias and selects the C built-in.

The generic request uses the current server context. Without a valid connected server, it is not transmitted; the sending layer can report a not-connected error. Outbound [mangling](mangle_types.md), server encoding, and [on send_to_server](../on/send_to_server.md) can change or suppress transmission. The current normal sending layer truncates a resulting statement to 510 bytes and appends CRLF rather than splitting it into several valid requests.

The command returns no function value or generic local success result. Replies pass through EPIC's normal incoming protocol handlers and hooks, which may update local state or change what is displayed. Server policy remains authoritative for acceptance and reply content.

Use [quote](../commands/quote.md) or [xquote](../commands/xquote.md) when an explicit raw IRC command is needed; those have their own options and limits. Enabling [dispatch_unknown_commands](../set/dispatch_unknown_commands.md) also forwards unknown commands, but that setting is separate from registered pass-through commands.

## Source references

- `source/commands.c`: command registrations, `send_comm()`, `send_2comm()`, alias precedence, and unknown-command dispatch.
- `source/server.c`: outgoing encoding, mangling, connection checks, line limits, and send hooks.
- `source/parse.c` and `source/numbers.c`: incoming commands and numeric replies.
