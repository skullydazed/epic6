# finger

## Synopsis

```text
/ctcp <target> finger
```

## Description

Ask another client for more detailed information about its user. The information is controlled by that client and its user, so it may be incomplete, customized, or inaccurate. A client may omit or disable the responder, and a reply is not guaranteed.

`<target>` is normally a nickname. The [ctcp](../commands/ctcp.md) command also accepts a channel or `*` for the current channel, where multiple clients may respond. There are no `finger`-specific options or required arguments. In ordinary use, the request travels in an IRC `privmsg` and a supporting client replies in a CTCP `notice`. Delivery, filtering, and errors depend on the server and destination.

## EPIC6 response behavior

The responder is provided by [script/ctcp](../../script/ctcp), loaded by [script/builtins](../../script/builtins), rather than by a C built-in handler. A setup that does not load that script needs `/load ctcp` to install it. The supplied responder ignores additional request arguments and constructs this reply:

```text
name (user@host) Idle seconds second(s)
```

It chooses the fields as follows:

- `name`: the nonempty `IRCFINGER` environment variable, otherwise [default_realname](../set/default_realname.md), otherwise `Esteemed EPIC User`.
- `user`: the nonempty `IRCUSER` environment variable, otherwise the portion before `@` in the current server's recorded userhost (`$X`).
- `host`: the portion after `@` in that recorded userhost. It is not independently discovered by this handler.
- `seconds`: the client's input idle time (`$E`) in whole seconds, rather than a server-reported idle value.

Replies normally pass through [on ctcp_reply](../on/ctcp_reply.md) and display as `CTCP FINGER reply from sender: reply-text` unless scripts suppress or customize display. Request hooks or deactivating the registered CTCP can prevent automatic responses. Automatic replies are subject to `no_ctcp_flood` reply throttling; long replies can be truncated to available IRC message space.

## Example

```text
/ctcp john finger
```

If John's client supports and permits this request, it returns its configured user information.

## Source references

- [script/ctcp](../../script/ctcp): `do_ctcp_finger_request` and request-handler registration.
- [script/builtins](../../script/builtins): loads the CTCP script.
- [source/functions.c](../../source/functions.c): `$X` reads the server userhost; `$E` calculates client input idle time.
- [source/screen.c](../../source/screen.c): input processing updates the idle timestamp.
- [source/commands.c](../../source/commands.c) and [source/ctcp.c](../../source/ctcp.c): CTCP sending, reply hooks/display, encoding, and reply throttling.
