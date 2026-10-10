# quote

Send an IRC protocol statement directly to the current server.

## Synopsis

```text
/quote irc-statement
```

## Description

`quote` behaves like [xquote](xquote.md) without options. It forwards the whole argument text through the normal server sending layer without interpreting the IRC command or validating its parameters. Include the protocol command and any required separators yourself, including a colon before a trailing parameter containing spaces.

The built-in accepts no options. In particular, text beginning with `-server`, `-all`, or `-url` is forwarded as statement text; those flags belong to `xquote`. With no text, `quote` silently does nothing. The command is always registered and returns no value or local success echo. Server replies and errors are handled normally.

The request uses the current server reference. It does not fall back to the primary server when no current server context exists. A valid but closed server record normally produces the sending layer's not-connected diagnostic, subject to the `disconnect` hook; without a valid server record, nothing is sent. Use `xquote -server` for explicit server selection.

## Encoding and limits

Direct protocol access is still subject to EPIC's normal outgoing processing. Outbound line mangling applies, the entire statement is recoded using the server's [encoding](encoding.md), and the `send_to_server` [on](on.md) hook can suppress transmission. Unlike [msg](msg.md), this path does not separately recode a message body for its recipient.

The current sending layer truncates the resulting statement to 510 bytes and appends a CRLF line terminator. It does not split long statements or preserve arbitrary byte data. Literal control characters, including embedded CR or LF, are subject to the configured line processing and can have protocol effects if retained. Supply one ordinary IRC statement; use `xquote -url` when you specifically need its separate raw-byte interface, including responsibility for wire terminators.

`quote` bypasses built-in command bookkeeping before sending. Prefer ordinary commands such as [nick](nick.md), [join](join.md), and `msg` when they provide the operation you need: sending a protocol request directly does not perform their pending-state or other local preparation. Server support, authorization, and protocol rules determine whether a request succeeds.

## Example

Send a private message to `foo`:

```text
/quote PRIVMSG foo :text of message
```

The colon makes the message body a single trailing IRC parameter. This does not provide the ordinary outgoing `msg` echo or saved-target handling.

## Source

Registration and the `quotecmd` distinction between `quote` and `xquote` are in [source/commands.c](../../source/commands.c). Current-server transmission, connection checks, line processing, encoding, hooks, and wire limits are in [source/server.c](../../source/server.c).
