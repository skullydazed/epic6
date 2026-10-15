# action

## Synopsis

```text
/ctcp <target> action <action-description>
```

## Description

CTCP `action` carries an action description, as used by [describe](../commands/describe.md) and [me](../commands/me.md). Prefer those commands for ordinary use: they provide action-specific local display and the [on send_action](../on/send_action.md) hook. Sending `/ctcp <target> action ...` directly sends the protocol message but does not invoke `on send_action` or provide that action-specific local echo.

`<target>` is the destination accepted by [ctcp](../commands/ctcp.md), normally a nickname or channel. `*` selects the current channel, not the current query. The remaining text is the action description; there are no action-specific options. Use a nonempty description: the incoming built-in handler silently ignores an empty one.

In ordinary use, EPIC sends a CTCP-delimited `action` payload inside an IRC `privmsg`. The generic `ctcp` command instead uses `notice` when invoked while processing a private-message event and rejects invocation from a notice event with `You may not use the CTCP command from a NOTICE!`. A live connection, valid target, and any required channel permissions are needed for delivery; server policy determines rejection or filtering. Long outgoing payloads are truncated to the sender's available IRC message space.

## Receiving actions

The built-in handler is enabled by default and handles both request and reply forms without sending an automatic response. A received nonempty action invokes [on action](../on/action.md) with the sender, destination, and description. If that hook permits normal display, EPIC prints:

- `* sender description` for the current channel.
- `* sender:channel description` for another channel.
- `*> sender description` for a non-channel destination.

Action output uses the `action` message level. Script CTCP handlers or hooks can replace or suppress the normal behavior. The general CTCP parser can also discard payloads before this handler runs, including messages with more than eight CTCP delimiter characters and messages to a `#` channel the client has not joined.

## Examples

```text
/me waves
/describe #example waves
/ctcp #example action waves
```

The first sends an action to the current window's target. The second sends one to `#example` with the command's action echo and hook. The third sends the CTCP directly through the generic command.

## Source references

- [source/ctcp.c](../../source/ctcp.c): `init_ctcp`, `do_atmosphere`, `do_ctcp`, and `send_ctcp` implement registration, receiving, parser limits, encoding, framing, and sending.
- [source/commands.c](../../source/commands.c): `ctcpcmd`, `describe`, and `mecmd` implement destination selection and distinguish generic CTCP sending from action-specific hooks and echoes.
