# describe command

## Synopsis

```text
/describe <nickname|channel|*> <action-description>
```

## Description

`describe` sends an action: a third-person description of what you are doing, as though someone is watching you. You can send it to an individual or a channel. For example, a recipient may display `foobar is eating pizza` rather than an ordinary chat message.

The target and a nonempty description are required. Missing either produces a usage message. There are no options. The target is read as one whitespace-delimited token, and the rest of the text is the description. Normal command expansion can occur before the handler receives it.

`*` substitutes the current window's channel, not its query target. If there is no current channel, it becomes the sink target `0`: no message is transmitted, although the local action hook and echo can still run. Comma-separated target lists and other supported destinations use EPIC's ordinary [`msg`](msg.md) sending layer.

The command sends a CTCP `ACTION` request framed with control-A delimiters and carried by IRC `PRIVMSG`. It always selects request mode; unlike [`ctcp`](ctcp.md), it does not switch to reply mode during PRIVMSG handling or refuse the command during NOTICE handling. This describes the implementation, not a guarantee that a server permits every use.

After attempting the send, EPIC offers the original target and description to the `send_action` hook and, unless that hook suppresses it, displays:

```text
* -> target: your-nickname description
```

Suppressing this echo does not cancel the send, which has already been attempted. The `send_ctcp` hook also observes the outgoing framed message, but its return value does not cancel it. A local echo is not confirmation of delivery. See [`on`](on.md).

## Related behavior and restrictions

The transmitted payload is the same kind of CTCP action as `/ctcp target action description`. Local echo and message-context behavior differ between those commands. [`me`](me.md) sends the same kind of action to the current window's conversation target; `describe` supplies the target explicitly.

The command is available in every build and needs a usable connection for IRC destinations. Long descriptions can be truncated by the CTCP sender's fixed buffer. Payload text is recoded for its destination. Special `=nickname` DCC routing is not implemented in the current sending path, despite that notation appearing in the handler's usage message.

Recipients' clients and server policies determine how an action is displayed or whether it is delivered. CTCP ACTION normally has no automatic reply. Unsupported requests may be ignored or answered with an error, depending on the recipient; this command does not wait for a response.

## Examples

If your nickname is `foobar`, send an action to a friend:

```text
/describe booya is eating pizza
```

Send it to a named channel or the current channel:

```text
/describe #blah is eating pizza
/describe * is eating pizza
```

Recipients may see something like:

```text
* foobar is eating pizza
```

## Source

See [source/commands.c](../../source/commands.c) for `describe()`, `mecmd()`, and `send_text()`. [source/ctcp.c](../../source/ctcp.c) implements framing and outgoing hooks in `send_ctcp()`, and default incoming action display in `do_atmosphere()`.
