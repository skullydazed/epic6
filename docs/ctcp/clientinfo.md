# clientinfo

## Synopsis

```text
/ctcp <target> clientinfo [<ctcp-command>]
```

## Description

Request information about another client's CTCP support. With no command argument, a supporting client can return a list of CTCP requests it supports. With an argument, it can return information about that request. Other clients may not support `clientinfo`, may disable it, or may return a different format; a reply is not guaranteed.

`<target>` is normally a nickname. The [ctcp](../commands/ctcp.md) command also accepts a channel or `*` for the current channel, which can cause multiple clients to respond. There are no `clientinfo`-specific options. In ordinary use, the request is sent as a CTCP-delimited IRC `privmsg` and the reply arrives as a CTCP `notice`. Delivery, filtering, and server errors depend on the IRC server and destination.

## EPIC6 response behavior

The responder is provided by [script/ctcp](../../script/ctcp), rather than a C built-in handler. [script/builtins](../../script/builtins) loads that script; a setup that does not load it needs `/load ctcp` to install these handlers.

With no argument, the supplied script returns the active CTCP names from `ctcpctl(active)`. With a registered, active command name, it returns that name in uppercase followed by its registered description. Command lookup is case-insensitive. Only the first command argument is used, and an inactive registered command receives no reply. The list describes the local registration state, including script-provided entries; it is not a guarantee that every listed name accepts user-generated requests.

Replies normally pass through [on ctcp_reply](../on/ctcp_reply.md) and appear as `CTCP CLIENTINFO reply from sender: reply-text` unless scripts suppress or customize display. Request hooks and CTCP activation settings can suppress responding. Outgoing replies are subject to `no_ctcp_flood` reply throttling, and long replies can be truncated to available IRC message space.

## Limitation

The current supplied responder asks `ctcpctl` about the requested name without first checking that it is registered. The C `get` path does not guard the failed lookup before accessing the entry, so requesting an unknown name from an EPIC6 responder can crash that client. Request descriptions only for names that responder has advertised.

## Examples

List the requests John's client advertises:

```text
/ctcp john clientinfo
```

Ask for information about `version` after confirming it is advertised:

```text
/ctcp joebob clientinfo version
```

## Source references

- [script/ctcp](../../script/ctcp): `do_clientinfo_request` and registration of its request handler and description.
- [script/builtins](../../script/builtins): loads the CTCP script.
- [source/ctcp.c](../../source/ctcp.c): `function_ctcpctl`, `lookup_ctcp`, `do_ctcp`, and `send_ctcp` implement registration lookup, reply display, framing, and reply throttling.
- [source/commands.c](../../source/commands.c): `ctcpcmd` parses target, request name, and arguments.
