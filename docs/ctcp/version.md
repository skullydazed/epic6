# version

## Synopsis

```text
/ctcp <target> version
```

## Description

Ask another client which IRC client and version it is using. The returned information can be customized or falsified by that client or its scripts; it is not a verified software identification. A client may disable or omit the responder, so no reply is guaranteed.

`<target>` is normally a nickname. The [ctcp](../commands/ctcp.md) command also accepts a channel or `*` for the current channel, where multiple clients may respond. There are no `version`-specific options or required arguments. In ordinary use, the request travels in an IRC `privmsg` and the reply in a CTCP `notice`. Delivery, filtering, and errors depend on the server and destination.

## EPIC6 response behavior

The responder is supplied by [script/ctcp](../../script/ctcp), loaded by [script/builtins](../../script/builtins), rather than by a C built-in handler. A setup that does not load it needs `/load ctcp` to install the handler.

The supplied script ignores additional request arguments and constructs this reply:

```text
ircII <client-version> - <client-information>
```

`<client-version>` is `$J`, the client's compiled version name. `<client-information>` is the current [client_information](../set/client_information.md) setting, whose default comes from the build's `IRCII_COMMENT`. The handler does not independently query or append the operating system name or version; such information appears only if included in the configured text or a customized handler.

Replies normally pass through [on ctcp_reply](../on/ctcp_reply.md) and display as `CTCP VERSION reply from sender: reply-text` unless scripts suppress or customize display. Request hooks or deactivating the registered CTCP can prevent responding. Automatic replies are subject to `no_ctcp_flood` reply throttling. Encoding conversion and IRC message-space limits can alter or truncate the transmitted text.

## Examples

```text
/ctcp john version
```

Request John's client identification. Customize the final part of your own supplied responder's reply with:

```text
/set client_information Running my own scripts.
```

## Source references

- [script/ctcp](../../script/ctcp): registration and reply construction for `version`.
- [script/builtins](../../script/builtins): loads the CTCP script.
- [source/functions.c](../../source/functions.c) and [source/irc.c](../../source/irc.c): `$J` returns the compiled `irc_version` string.
- [source/vars.c](../../source/vars.c): `client_information` registration and build-provided default.
- [source/commands.c](../../source/commands.c) and [source/ctcp.c](../../source/ctcp.c): request sending, reply hooks/display, encoding, and reply throttling.
