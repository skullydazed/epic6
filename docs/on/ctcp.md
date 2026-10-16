# on ctcp

## Synopsis

```text
/on [<noise>]ctcp "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC has finished processing an incoming [CTCP](../commands/ctcp.md) request and reaches its generic display step. Use this event to customize the notification shown to the user. It runs after any CTCP request handler, so suppressing it cannot undo a reply or other processing that has already happened.

Use [on ctcp_request](ctcp_request.md) to intercept requests before their handlers. Incoming replies carried in `notice` use [on ctcp_reply](ctcp_reply.md) for generic display instead of this event. CTCP actions have their own [on action](action.md) event and normally bypass the generic CTCP notification.

The pattern matches all four fields below together, using the standard [on](../commands/on.md) matching and noise controls. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender's nickname. |
| `$1` | Destination as received, normally your nickname or a channel. |
| `$2` | CTCP command name as received. |
| `$3-` | CTCP arguments, possibly empty. |

Arguments normally undergo incoming character-set conversion. A registered handler can replace the arguments before this display event, so they are not guaranteed to be the original payload.

## Default behavior and limits

If the hook permits default display, EPIC prints `CTCP command from sender: arguments` for a request addressed to your nickname, or `CTCP command from sender to destination: arguments` otherwise, with its usual message prefix. The colon and argument text are omitted when there are no arguments. Output uses the `ctcp` level and the joined channel's context, or the sender's context for other destinations.

A suppressing hook, such as a serial-zero `^ctcp` hook, replaces only that generic line. It does not prevent CTCP processing or automatically send a response. Keep request handling in `ctcp_request` or a registered CTCP handler so that it occurs at the appropriate stage.

Not every request reaches this hook. Earlier suppression by `ctcp_request`, special handlers such as the built-in action handler, and parser limits can bypass it. Payloads in messages with more than eight CTCP delimiter characters, destinations beginning with `$`, and `#` channels the client has not joined are discarded before request handling. After one CTCP reaches generic display, further CTCP payloads in the same IRC message are discarded without processing; suppressing the display does not change that limit.

EPIC6 initially registers only the built-in action handler. Other CTCP behavior can be supplied by scripts; do not infer that a notification means EPIC sent a reply.

## Example

Customize the normal CTCP message:

```text
on ^ctcp * (sender, recvr, ctcp, args) {
    if (*args) {
        @args = ': ' ## args;
    };
    if (ischannel($recvr)) {
        xecho -b $sender sent to $recvr a CTCP $ctcp$args;
    } else {
        xecho -b $sender sent you a CTCP $ctcp$args;
    };
};
```

## Source references

- [source/ctcp.c](../../source/ctcp.c): `do_ctcp` orders request handling and generic display, applies parser limits, and invokes the hook; `init_ctcp` registers the built-in action handler.
- [source/parse.c](../../source/parse.c): `p_privmsg` passes requests to `do_ctcp`; `p_notice` passes replies.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard matching and suppression.
