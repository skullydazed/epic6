# on error

## Synopsis

```text
/on [<noise>]error "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when the server sends an IRC `error` message and EPIC dispatches it to the error-message handler. Such a message often explains why the server is closing the connection, but its text and meaning depend on the server.

This hook handles the named IRC command, not every error that EPIC encounters. Numeric error replies, local command errors, and socket failures have separate handling. An earlier suppressing [on raw_irc](raw_irc.md) hook can prevent the line from reaching this event.

Use the standard [on](../commands/on.md) pattern and noise controls. The pattern matches the sender and complete error text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Message sender from the IRC prefix, normally the server; `*` when no usable sender is present. |
| `$1-` | Error message text. |

EPIC joins the message's parameters into the error text before invoking the hook. A missing error parameter is treated as a malformed message and does not generate this event. An explicitly empty parameter can still reach the handler. No numeric error code or server refnum is included in these arguments.

## Default behavior

If the hook permits default handling, EPIC displays `sender error-text` with its usual message prefix. A suppressing hook, such as a serial-zero `^error` hook, prevents this line and can supply replacement output.

The error handler itself does not close the connection, reconnect, or send an IRC reply. Suppressing the line does not stop a server from closing its socket or suppress subsequent connection-lifecycle notifications. Use [on server_lost](server_lost.md) or related connection events when tracking actual closure.

## Example

```text
on ^error * (sender, message) {
    xecho -b Server error from $sender: $message;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_error` assembles the text, substitutes `*` for a missing or unusable sender, invokes the hook, and prints default output; `parse_server` runs `raw_irc` before command dispatch.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard matching and suppression.
- [source/server.c](../../source/server.c): socket closure and reconnect handling occur separately from the IRC error-message handler.
