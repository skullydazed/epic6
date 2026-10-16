# on ctcp_reply

## Synopsis

```text
/on [<noise>]ctcp_reply "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when an incoming [CTCP](../commands/ctcp.md) reply reaches EPIC's generic reply-display step. Replies are CTCP payloads carried in IRC `notice` messages. EPIC need not have sent a corresponding request: an unsolicited payload can trigger this event too.

The hook runs after any registered CTCP reply handler. Special handlers can bypass it; for example, the built-in CTCP action handler invokes [on action](action.md) instead. This hook does not receive CTCP requests carried in `privmsg`; those use [on ctcp_request](ctcp_request.md) and [on ctcp](ctcp.md).

Use the standard [on](../commands/on.md) pattern and noise controls. The pattern matches all four fields below together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Nickname of the sender. |
| `$1` | Actual destination of the reply as received. |
| `$2` | CTCP command name as received. |
| `$3-` | Reply arguments, possibly empty; their meaning depends on the CTCP command in `$2`. |

Arguments normally undergo incoming character-set conversion. A registered reply handler can replace them before this event, so they are not guaranteed to be the original payload. No check here establishes that a reply matches an outstanding request.

## Default behavior and limits

If the hook allows default display, EPIC prints `CTCP command reply from sender: arguments` with its usual message prefix. The colon remains even when arguments are empty. Output uses the `ctcp` message level and the joined channel's context, or the sender's context for other destinations.

A suppressing hook, such as a serial-zero `^ctcp_reply` hook, prevents this generic line. It cannot undo processing already performed by a registered reply handler and does not itself send anything to the server.

Parser limits can prevent the event: messages with more than eight CTCP delimiter characters, destinations beginning with `$`, and `#` channels the client has not joined are discarded before handler processing. After one payload reaches generic reply display, later CTCP payloads in that IRC message are discarded without processing, even if the hook suppresses the line.

## Example

Customize the appearance of reply strings:

```text
on ^ctcp_reply * (sender, recvr, ctcp, stuff) {
    echo *** Reply from $sender to $recvr for CTCP $ctcp request: $stuff;
};
```

## Automatic responses

Do not automatically answer replies with [msg](../commands/msg.md), [ctcp](../commands/ctcp.md), or [notice](../commands/notice.md): responding to notices can create reply loops. The built-in `ctcp` command specifically refuses invocation while processing a notice, printing `You may not use the CTCP command from a NOTICE!`. This does not imply that every possible script or outgoing command is blocked in the same way.

## Compatibility

`$1` has contained the actual reply destination since 15 November 2001, CVS commit level 140. Scripts written for the earlier argument format must account for this field.

## Source references

- [source/ctcp.c](../../source/ctcp.c): `do_ctcp` runs reply handlers, applies limits, and invokes generic reply display; `init_ctcp` registers the special action handler.
- [source/parse.c](../../source/parse.c): `p_notice` sets notice-processing state and calls `do_ctcp` in reply mode.
- [source/commands.c](../../source/commands.c): `ctcpcmd` rejects CTCP commands invoked from a notice.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard matching and suppression.
