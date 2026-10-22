# errmsg

## Synopsis

```text
/ctcp <target> errmsg [<text>]
```

## Description

Send an `ERRMSG` CTCP request with optional text. EPIC6's supplied handler returns that text in an `ERRMSG` reply. Despite its name, this handler does not detect an error, validate the text, or generate an error report; it echoes the supplied arguments, like [echo](echo.md). Other clients may handle the request differently or not answer.

`<target>` is normally a nickname. The [ctcp](../commands/ctcp.md) command also accepts a channel or `*` for the current channel. There are no `errmsg`-specific options. Omitting `<text>` sends a request without argument text.

## EPIC6 behavior

The responder is registered by [script/ctcp](../../script/ctcp), which [script/builtins](../../script/builtins) loads. If your setup does not load it, `/load ctcp` installs the handler.

For an incoming request, the handler sends the argument text back to the sender under the same CTCP name. An empty request produces an empty reply. Ordinary outgoing requests use IRC `PRIVMSG`; the automatic response uses `NOTICE`. Request hooks, handler deactivation, and reply throttling can prevent a response. Text encoding conversion and IRC message-space limits can alter or truncate the payload.

The script installs no special `ERRMSG` reply handler. Replies pass through [on ctcp_reply](../on/ctcp_reply.md) and normally display as `CTCP ERRMSG reply from sender: text`, unless a hook suppresses or customizes the display. See [ctcp](../commands/ctcp.md) for sending context and hook behavior.

## Example

```text
/ctcp john errmsg test message
```

If John's client uses the supplied EPIC6 handler and permits the response, it replies with `test message` under the CTCP name `ERRMSG`.

## Source references

- [script/ctcp](../../script/ctcp): `ERRMSG` request handler and description.
- [source/commands.c](../../source/commands.c): `ctcpcmd()` argument parsing and request/reply selection.
- [source/ctcp.c](../../source/ctcp.c): `do_ctcp()` dispatch and reply display; `send_ctcp()` framing, encoding, and reply throttling.
