# userinfo

## Synopsis

```text
/ctcp <target> userinfo
```

## Description

Ask another client for its user-supplied information text. An EPIC6 responder returns its [user_information](../set/user_information.md) setting, which can contain whatever text the user chooses. The reply is not verified personal information, and other clients may implement a different response or not answer at all.

`<target>` is normally a nickname. The [ctcp](../commands/ctcp.md) command also accepts a channel or `*` for the current channel, where multiple clients may respond. There are no `userinfo`-specific options or required arguments. In ordinary use, the request travels in an IRC `privmsg` and the reply in a CTCP `notice`. Delivery, filtering, and errors depend on the server and destination.

## EPIC6 response behavior

The responder is supplied by [script/ctcp](../../script/ctcp), loaded by [script/builtins](../../script/builtins), rather than by a C built-in handler. A setup that does not load it needs `/load ctcp` to install the handler.

The script ignores additional request arguments and replies with the current `user_information` value. The default is `EPIC6 -- Did we get lost again?`. An unset or empty value produces a reply with no argument text; it does not disable the responder.

Replies normally pass through [on ctcp_reply](../on/ctcp_reply.md) and display as `CTCP USERINFO reply from sender: text` unless scripts suppress or customize display. Request hooks or deactivating the registered CTCP can prevent responding. Automatic replies are subject to `no_ctcp_flood` reply throttling. Encoding conversion and IRC message-space limits can alter or truncate the transmitted text.

## Examples

Request John's configured information:

```text
/ctcp john userinfo
```

Set the text your own supplied responder returns:

```text
/set user_information Ask me about EPIC scripting.
```

## Source references

- [script/ctcp](../../script/ctcp): registration of the `userinfo` request handler and description.
- [script/builtins](../../script/builtins): loads the CTCP script.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): `user_information` registration and default.
- [source/commands.c](../../source/commands.c) and [source/ctcp.c](../../source/ctcp.c): request sending, reply hooks/display, encoding, and reply throttling.
