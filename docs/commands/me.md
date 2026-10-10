# me

Send an action description to the current conversation.

## Synopsis

```text
/me action-description
```

## Description

`me` describes what you are doing in the third person, as though someone is watching you. It selects the current window's query target first; if there is no query, it uses the current channel. A query therefore takes precedence even when the window also has a channel.

The whole argument text is the description. It must be nonempty; otherwise EPIC prints usage. With neither a query nor a channel, it reports `No target, neither channel nor query`. There are no options, and the built-in is always registered.

The description is sent as a CTCP `ACTION` request, framed with control-A delimiters and carried by IRC `PRIVMSG`. Destination [encoding](encoding.md) rules apply. Long descriptions can be truncated by the CTCP sender's fixed buffer. The command always selects request mode, including when called from a message hook; it does not automatically switch to a CTCP reply.

After attempting the send, EPIC offers the target and original description to the `send_action` hook. Unless that hook suppresses display, it echoes:

```text
* your-nickname action-description
```

This echo uses action-level output routing. Suppressing it does not cancel the request already attempted. The outgoing `send_ctcp` hook also observes the framed request, but its return value does not cancel sending. A local echo does not confirm delivery. See [on](on.md).

## Example

```text
/me doesn't want to go to work today
```

If your nickname is `foobar`, recipients may see:

```text
* foobar doesn't want to go to work today
```

## Related commands and restrictions

[describe](describe.md) supplies an explicit target and produces a different local echo. `/describe target description` and `/ctcp target action description` use the same kind of transmitted action, but their argument handling, local output, and message-context behavior differ. See [ctcp](ctcp.md).

IRC destinations need a usable server connection. Server policy determines delivery, and recipients' clients determine how the action appears. Unsupported CTCP actions may be ignored or handled differently; an error is not proof of a client compatibility problem. The command does not wait for a response.

## Source

Registration and `mecmd` are in [source/commands.c](../../source/commands.c). Query-before-channel target selection is in [source/window.c](../../source/window.c). Framing, recoding, outgoing hooks, and incoming action display are in [source/ctcp.c](../../source/ctcp.c), with message delivery through the sending helpers in [source/commands.c](../../source/commands.c) and [source/server.c](../../source/server.c).
