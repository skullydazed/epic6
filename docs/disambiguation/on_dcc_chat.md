# on dcc_chat (removed)

## Former synopsis

```text
/on [<noise>]dcc_chat "<pattern>" { <commands> }
```

## Description

The `dcc_chat` event formerly ran when a message arrived across a direct [DCC Chat](ctcp_chat.md) connection. Its default action formatted the message and displayed it to the screen.

EPIC6 has removed DCC Chat and this named hook. It cannot receive DCC Chat messages or generate this event through the former built-in implementation. There is no current default display or automatic-response behavior for this hook.

## Former parameters

| Parameter | Former meaning |
| --- | --- |
| `$0` | Sender's nickname. |
| `$1-` | Received message. |

These describe the removed interface, not parameters supplied by EPIC6.

## Compatibility

Attempting to register `/on dcc_chat ...` is rejected with `No such ON function: DCC_CHAT`. Retained references in scripts such as `xmsglog` do not restore the event or its transport. A script implementing its own direct connection must provide its own message handling.

For ordinary private IRC messages, see [on msg](../on/msg.md). Those messages travel through the IRC server and have different protocol and reply rules; they do not replace a direct DCC connection. The former observation that DCC Chat was outside IRC's automatic-reply rules applies only to the removed transport.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `dccctl`, and related functionality.
- [source/hook.c](../../source/hook.c): the hook table omits `dcc_chat`; `find_hook` rejects unknown named events.
- [include/hook.h](../../include/hook.h): current event identifiers contain no DCC Chat receive event.
- [source/commands.c](../../source/commands.c): current message sending has no former DCC Chat transport.
- [script/xmsglog](../../script/xmsglog): retains references to the removed receive and send events without implementing DCC Chat.
