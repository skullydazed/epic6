# away command

## Synopsis

```text
/away [-one | -all | --] [message]
```

With a nonempty message, request away status. Without a message, clear away status. `-one` is the default.

| Form | Effect |
| --- | --- |
| `/away -one I'm not here` | Set the current server's away message. |
| `/away -one` | Clear the current server's away message. |
| `/away -all Eating dinner` | Set the message for every existing server record. |
| `/away -all` | Clear it for every existing server record. |
| `/away -- -This message has dashes-` | Treat the dash-prefixed text as a message on the current server. |

The first token is interpreted as a flag if it begins with `-` or `/`. Flags are case-insensitive and checked by their first character after the prefix: `-a` selects all servers and `-o` selects one. Slash-prefixed forms such as `/all` also work. An unknown flag reports `AWAY: ... unknown flag` and makes no change. Only one flag token is consumed; the remainder is the message, not another list of options. Use `--` before a message beginning with `-` or `/`.

## Description

When you are away from IRC, you can tell the server so other people can find out. Servers typically return your away message when someone privately messages or WHOISes you. WHO replies commonly show `G` for “gone” instead of `H` for “here”. Those replies and their exact behavior are controlled by the server.

EPIC stores the requested away message separately for each server. For a registered connection, it sends `AWAY :message`; clearing the message sends `AWAY :`. Server replies 306 and 305 update EPIC's separately tracked confirmed away status. The command itself does not wait for confirmation, so a stored message is not proof that the server accepted the request.

The status-bar `%A` expando uses the `status_away` setting when that window's server has a stored away message. Display also depends on the status format and window visibility rules. The configured default text is ` (Away)`; it can be customized.

## Of note

- You remain away until you clear the message, another command or script clears it, or the server changes your status.
- EPIC resends a stored away message when registration completes after connecting or reconnecting to that server record.
- You can have a different message on each server. `-all` updates every existing server record, including disconnected ones; disconnected records retain the request for a later connection. It does not set a default for new server records created afterward.
- A server need not preserve your away status across network disruptions. Delivery and message length limits depend on the server; EPIC does not enforce a special 80-character away-message limit.
- With `-one`, an invalid current server produces `You are not connected to a server.` A disconnected but valid record can still store or clear its message. With no server records, `-all` changes nothing.

The command is available in every build. It refreshes the status display after changing the stored messages.

## Script-provided behavior

The bundled [builtins script](../../script/builtins) supplies the `auto_unmark_away` setting, defaulting to off. When enabled, its input hooks clear away status for ordinary typed text and for typed `/me`, `/msg`, `/notice`, and `/say` commands. This is script behavior, not automatic behavior of the C `away` command or every outgoing message.

To reduce repeated reports of other people's away messages, you can load the bundled [newaway script](../../script/newaway):

```text
/load newaway
```

It suppresses repeated away messages for a nickname when the message has not changed, comparing without regard to case, while still displaying away information during WHOIS. This affects received replies, not your own away state.

## Examples

To mark yourself away on the current server:

```text
/away I'm not here right now, I'll be back soon
```

To mark yourself away on all existing server records:

```text
/away -all I'm in the shower, back in 10 minutes
```

To clear away status on the current server or all existing records:

```text
/away
/away -all
```

## History

The `away` command first appeared in ircII.

## Source

See [source/commands.c](../../source/commands.c) for `awaycmd()` and its flag parsing. [source/server.c](../../source/server.c) implements stored messages, confirmed status, server-record validity, and reconnect restoration. [source/numbers.c](../../source/numbers.c) handles replies 301, 305, and 306. [source/status.c](../../source/status.c) implements `%A`; [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h) define `status_away` and its default.
