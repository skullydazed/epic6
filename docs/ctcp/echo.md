# echo

## Synopsis

```text
/ctcp <target> echo [<text>]
```

## Description

Ask another client to return the supplied text in a CTCP reply. This can confirm that a supporting client receives and answers CTCP requests. It does not itself calculate elapsed time; use [ping](ping.md) for the supplied timestamp-based timing behavior.

`<target>` is normally a nickname. The [ctcp](../commands/ctcp.md) command also accepts a channel or `*` for the current channel, where multiple clients may respond. The remaining text is the payload, with no `echo`-specific options. Omitting it sends an empty `echo` request.

In ordinary use, the request is sent in an IRC `privmsg`, and a supporting client replies with a CTCP `notice`. Delivery, filtering, and errors depend on the server and the other client's configuration. Many clients disable or omit automatic echo responses to limit unwanted reply traffic; no reply is guaranteed.

## EPIC6 behavior

The automatic responder is provided by [script/ctcp](../../script/ctcp), loaded by [script/builtins](../../script/builtins). A setup that does not load it needs `/load ctcp` to install this handler. The script sends the received argument text back to the sender under the same CTCP name; an empty request produces a reply without argument text. It does not install a special `echo` reply handler or timing calculation.

Replies normally pass through [on ctcp_reply](../on/ctcp_reply.md) and display as `CTCP ECHO reply from sender: text`, unless scripts suppress or customize display. Request hooks or deactivating the registered CTCP can prevent automatic responses.

Incoming text is subject to EPIC's encoding conversion, and outgoing text is encoded for its destination, so this is a text echo rather than a guarantee of identical raw bytes. Long payloads can be truncated to available IRC message space. Automatic replies are subject to `no_ctcp_flood` reply throttling; this setting does not throttle outgoing requests.

## Example

```text
/ctcp john echo hello there
```

If John's client supports and permits the request, it replies with `hello there`.

## Source references

- [script/ctcp](../../script/ctcp): registration of the `echo` request handler and description.
- [script/builtins](../../script/builtins): loads the CTCP script.
- [source/commands.c](../../source/commands.c): `ctcpcmd` parses the request and chooses request/reply framing.
- [source/ctcp.c](../../source/ctcp.c): `do_ctcp` processes hooks and displays replies; `send_ctcp` handles encoding, message limits, and reply throttling.
