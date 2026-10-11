# xquote command

## Synopsis

```text
/xquote [-server server-description] [-url] -- irc-statement
/xquote -all irc-statement
```

## Description

`xquote` sends an IRC protocol statement without interpreting its command or validating its parameters. It is [`quote`](quote.md) with server-selection and raw-byte options. Include the protocol command and required separators yourself, including a colon before a trailing parameter containing spaces. There is no built-in prohibition on particular protocol commands.

Normal sending still applies server encoding, outbound line mangling, the send_to_server hook, and the current 510-byte statement limit before appending CRLF. It does not separately recode a message body for its recipient, split long requests, or provide ordinary [`msg`](msg.md) local echo and saved-target handling.

## Options

| Option | Effect |
| --- | --- |
| `-server description` or `-s description` | Select an existing open server by description/reference. Invalid or closed selection reports `XQUOTE: Server number is not connected` and stops. It does not create or connect a server. |
| `-url` or `-u` | URL-decode the remaining text and send the resulting bytes through the raw sending interface. |
| `-all` or `-a` | Immediately send all remaining text normally to every registered server and return. Put this last among options. |
| `--` | End option processing. |

Options are case-insensitive and recognized by first letter; either `-` or `/` prefixes are accepted. An unknown prefixed token, including `--`, is consumed and ends option processing. Consequently, a misspelled option does not remain part of the statement. Option values use whitespace-delimited parsing without quote removal. Missing server-selection data silently ends the command.

`-all` does not continue parsing options: a later `-server` token becomes part of the transmitted statement. It ignores any earlier URL-mode selection and uses normal sending, not raw bytes. A preceding invalid `-server` still aborts before `-all` can run.

## Raw bytes and restrictions

URL mode sends exactly the decoded byte buffer, including embedded zero bytes when present. It bypasses normal encoding, mangling, send_to_server hooks, automatic CRLF, and the normal statement truncation. Supply encoded wire terminators yourself, such as `%0D%0A` for CRLF. Repeated `-url` options select the same single decoding operation rather than multiple passes. A decoding failure produces a diagnostic.

The raw sender uses the selected connection's ordinary socket or TLS transport; it does not bypass TLS. A closed or invalid server record can cause raw sending to do nothing without a connection diagnostic. A write failure can trigger normal connection-reset handling, and successful invocation does not guarantee every requested byte was delivered.

Without explicit selection, the command uses the current server reference and does not fall back to the primary server. It changes no current-window or server-context selection. Normal sending to a closed valid record can report a not-connected diagnostic. No text in normal mode silently does nothing. The built-in is always registered and has no function return value or local success confirmation; server replies and errors are handled normally.

Direct protocol requests bypass the local preparation performed by commands such as [`nick`](nick.md), [`join`](join.md), and [`userhost`](userhost.md). Prefer those commands when available, particularly for reply queues. Server support, permissions, and protocol rules determine whether a request succeeds.

## Examples

Send a message through an existing open server numbered 2:

```text
xquote -server 2 -- PRIVMSG foo :text of message
```

Send an ordinary query to every registered server:

```text
xquote -all VERSION
```

Send the same query as explicit raw bytes, including its terminator:

```text
xquote -url -- VERSION%0D%0A
```

## Source

[source/commands.c](../../source/commands.c) implements the `xquote` branch of `quotecmd()`, options, and decoding. [source/server.c](../../source/server.c) implements server lookup, normal sending, and `send_to_aserver_raw()`. [source/ircaux.c](../../source/ircaux.c) implements URL transformation.
