# on send_dcc_chat (removed)

## Former synopsis

```text
/on [<noise>]send_dcc_chat "<pattern>" { <commands> }
```

## Description

The `send_dcc_chat` event formerly reported a message sent to another client across a direct [DCC Chat](ctcp_chat.md) connection. Its arguments were the recipient's nickname in `$0` and the outgoing text in `$1-`.

EPIC6 has removed the DCC Chat transport and this named hook. It cannot send a message through that former transport or generate this event. There is no current DCC Chat send display or send action to control through it.

## Compatibility

Attempting to register `/on send_dcc_chat ...` is rejected with `No such ON function: SEND_DCC_CHAT`. Retained references in scripts such as `xmsglog`, `speak.irc`, and tab-completion scripts do not restore the event or its transport.

The historical `/msg =nickname ...` syntax does not select a DCC Chat connection in EPIC6. For ordinary private IRC messages, use [msg](../commands/msg.md) and its [on send_msg](../on/send_msg.md) event. Those messages travel through the IRC server and have their own routing, arguments, and suppression rules; they do not establish a direct connection.

Scripts implementing their own direct chat transport must provide their own outgoing-message handling and notifications. The removed hook is not a built-in integration point for that implementation.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `dccctl`, and related functionality.
- [source/hook.c](../../source/hook.c): the named-hook table omits `send_dcc_chat`; `find_hook` rejects unknown event names.
- [include/hook.h](../../include/hook.h): current event identifiers contain no DCC Chat send event.
- [source/commands.c](../../source/commands.c): the current message-sending path has no former DCC Chat transport.
- [script/xmsglog](../../script/xmsglog): retains a reference to the removed send event without implementing the transport.
