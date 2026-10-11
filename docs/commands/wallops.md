# wallops command

## Synopsis

```text
/wallops message
```

## Description

`wallops` sends a network-wide wallops message through your IRC server. Traditionally, recipients are users with user mode `+w` enabled. The server determines actual recipients and privileges; on many networks, sending is restricted to IRC operators or disabled for ordinary users.

EPIC sends `WALLOPS :message`, using all supplied argument text as one trailing parameter. There is no target argument, client-side option parsing, or local operator check. Quotes are part of the message rather than grouping syntax. With no arguments, it sends an empty trailing message for the server to accept or reject.

The whole IRC line uses the server's outbound encoding, rather than a channel- or nickname-specific message encoding. The command temporarily uses the wallop output context while sending, then restores the previous context. It has no normal local echo or function return value; normal outbound server hooks and line limits still apply.

## Output and restrictions

An IRC connection is required to send, and the server can reject the command or impose message and rate limits. Enabling `+w` to receive wallops does not itself grant permission to send them. EPIC is always built with this command, independently of server support.

Incoming wallops are handled separately. The wallop hook receives sender, an `S` marker for a sender containing a dot or `*` otherwise, and message text. Unless handled or suppressed, EPIC displays `!sender! message` for the former or `!sender*! message` for the latter. These markers follow the client's sender-name heuristic, not a verified privilege check. A message beginning with `OPERWALL - ` is first offered to the operwall hook without that prefix; if that hook suppresses it, the wallop hook is not called. See [`on`](on.md) for hook definitions.

## Example

When your server permits it:

```text
/wallops Network maintenance begins shortly
```

The server controls delivery; sending successfully does not guarantee that every connected user sees the message. [`wallchops`](wallchops.md) is a separate request intended for channel operators.

## Source

[source/commands.c](../../source/commands.c) registers `wallops` and implements `e_wallop()` without a command-name branch. [source/server.c](../../source/server.c) implements server encoding and sending. [source/parse.c](../../source/parse.c) implements incoming `p_wallops()` hooks and display.
