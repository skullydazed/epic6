# send

Send text to the current query, falling back to the current channel.

## Synopsis

```text
/send [text]
```

## Description

`send` sends the entire argument text to the current window's current target: its [query](query.md), if one exists, or its current channel otherwise. This is the same target priority used for ordinary input text and [me](me.md). With neither a query nor a channel, it silently does nothing.

[say](say.md) reverses that priority, choosing the channel before the query. Use [msg](msg.md) when you want to specify a different destination explicitly.

There are no options or target argument. A nickname, dash-prefixed word, or leading slash in the supplied text is part of the body, rather than an explicit recipient or command. For example, `/send /help` sends `/help` to an IRC current target; it does not execute the help command. Ordinary surrounding script expansion can still occur before the handler receives that text.

A query can also select a process, EPIC command, file, or other destination supported by the shared sending path. Those destinations interpret the body according to their own rules. A query of `/echo`, for example, runs `echo` with the sent text as arguments. See `msg` for destination details; historical `=nickname` notation does not provide DCC-chat routing in the current sending path.

## Output and restrictions

IRC destinations receive `PRIVMSG`. Private messages ordinarily echo `*target*> text` through the `send_msg` hook; channel messages echo `channel> text` through `send_public`. Suppressing these [on](on.md) hooks suppresses local display, not transmission. Nested sends can suppress hooks/output or be refused to limit recursion. Local output does not confirm delivery.

Destination [encoding](encoding.md), byte-based message splitting, and server line limits apply to IRC messages, so long text can be split or truncated. This built-in does not automatically switch to a notice during incoming-message handling. See [notice](notice.md).

Empty text sends nothing to ordinary destinations but sends a blank input line to a process query. IRC destinations require a usable connection and remain subject to server restrictions. Local destinations have their own requirements. The command is always registered, prints no missing-target usage error, and returns no result value.

## Examples

Send a line to the current conversation:

```text
/send hello there
```

If both a query and channel exist, the query receives it. To send to the channel instead:

```text
/say hello channel
```

## Source

Registration, `send_to_query_first`, ordinary input dispatch, and shared `send_text` behavior are in [source/commands.c](../../source/commands.c). Query/channel target selection is in [source/window.c](../../source/window.c), recoding in [source/recode.c](../../source/recode.c), and server transmission in [source/server.c](../../source/server.c).
