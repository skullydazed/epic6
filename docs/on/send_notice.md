# on send_notice

## Synopsis

```text
/on [<noise>]send_notice "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered by the ordinary outgoing [notice](../commands/notice.md) sender when that call enables send hooks. Both channel and non-channel notice targets use this event. It controls local echo before the actual send loop, rather than confirming server delivery.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches destination and original text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Destination currently being processed, normally a nickname or channel. |
| `$1-` | Original message text before outgoing character-set conversion. |

Comma-separated destinations are processed separately. The event sees the full original text once per eligible destination before encoded text can be split into multiple outgoing payload chunks. Empty text, sink target `0`, and destinations handled outside ordinary IRC sending can bypass it.

## Default behavior and effects

If the hook permits default handling, EPIC displays `-target-> message`. A suppressing hook, such as a serial-zero `^send_notice` hook, prevents that local echo but does not cancel the outgoing notice. The send loop follows regardless of the hook's default-handling return value. Changing handler variables does not replace the already prepared encoded payload.

Both channel and non-channel branches use the destination's output context at the `notice` message level and the same local format. After attempting the send loop, EPIC records the destination as the server's most recent sent nickname, even when echo was suppressed.

The hook can run before a later connection error or server rejection prevents delivery. See [on send_to_server](send_to_server.md) for the separate final server-line interface and its suppression contract. This event does not itself send an additional response.

## Limits and recursion

Internal callers can disable ordinary send hooks. In particular, the CTCP sender disables them for its framed messages, so CTCP replies do not automatically generate `send_notice`; see [on send_ctcp](send_ctcp.md). This is not a complete event stream for every outgoing notice wire line.

The hook is marked nonrecursive. The ordinary sender also disables send hooks for a nested send call and refuses deeper ordinary recursion. A handler can still attempt a nested send, so avoid sending the same notice again merely to replace its echo.

## Example

Replace local outgoing notice display:

```text
on ^send_notice "*" (target, message) {
    echo Notice to $target: $message;
};
```

## Source references

- [source/commands.c](../../source/commands.c): `send_text` classifies channel and non-channel notices into the same hook, invokes it before chunked sending, controls local echo, records the destination, and enforces recursion handling.
- [source/ctcp.c](../../source/ctcp.c): `send_ctcp` sends framed notices with ordinary send hooks disabled.
- [source/hook.c](../../source/hook.c): the event registration sets the nonrecursion flag; dispatch implements matching and default-echo suppression.
