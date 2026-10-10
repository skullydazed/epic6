# say

Send text to the current channel, falling back to the current query.

## Synopsis

```text
/say [text]
```

## Description

`say` sends the whole argument text to the current window's current channel. It is useful when that window also has a [query](query.md): ordinary input normally goes to the query, but `say` chooses the channel first.

If the window has no current channel, the current query becomes the destination. With neither channel nor query, the command silently does nothing. There are no options or explicit target argument. A leading word that looks like a nickname or flag is still message text. Use [msg](msg.md) for a different explicit destination, or change the current channel with the `switch_channels` binding through [bind](bind.md) or [parsekey](parsekey.md).

[send](send.md), [me](me.md), and ordinary input use the opposite priority: query first, then channel. A non-IRC query, such as an [exec](exec.md) process or `/command` destination, follows the shared sending rules described by `msg`; it is only used by `say` when no current channel exists.

## Output and restrictions

IRC destinations receive a `PRIVMSG`. Outgoing channel text uses the `send_public` hook and normally echoes `channel> text`; private text uses `send_msg` and normally echoes `*target*> text`. Suppressing those [on](on.md) hooks suppresses the echo, not transmission. Recipient clients determine how they display the message; local output does not confirm delivery. Nested sends can suppress hooks/output or be refused to limit recursion.

Destination [encoding](encoding.md) applies. Long IRC messages use the shared byte-based splitting and server line limits, so text can be split or truncated. The command does not automatically switch to a notice inside an incoming-message hook. See [notice](notice.md).

Empty text sends nothing to ordinary destinations, but can send a blank line to a process query. The built-in is always registered and returns no value. IRC destinations require a usable connection and are subject to server membership, modes, and rate limits. The command does not perform a separate connection or delivery check before selecting the target.

## Example

Send a message to the current channel even while querying someone:

```text
/say hello all!
```

Other clients might display:

```text
<your_nick> hello all!
```

EPIC's default channel echo includes the actual target, for example:

```text
#channel> hello all!
```

## Source

Registration, `send_to_channel_first`, `send_to_query_first`, and shared `send_text` behavior are in [source/commands.c](../../source/commands.c). Current-channel/query selection is in [source/window.c](../../source/window.c), with recoding in [source/recode.c](../../source/recode.c) and transmission in [source/server.c](../../source/server.c).
