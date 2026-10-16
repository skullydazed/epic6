# time

## Synopsis

```text
/ctcp <target> time
```

## Description

Ask another client for its local date and time. This differs from the [time](../commands/time.md) command, which queries an IRC server. The answer comes from the remote client's clock and configuration; not every client supports or permits this request, and a reply is not guaranteed.

`<target>` is normally a nickname. The [ctcp](../commands/ctcp.md) command also accepts a channel or `*` for the current channel, where multiple clients may respond. There are no `time`-specific options or required arguments. In ordinary use, the request is sent in an IRC `privmsg` and a supporting client replies in a CTCP `notice`. Delivery, filtering, and errors depend on the IRC server and destination.

## EPIC6 response behavior

The responder is supplied by [script/ctcp](../../script/ctcp), loaded by [script/builtins](../../script/builtins), rather than by a C built-in handler. A setup that does not load it needs `/load ctcp` to install the handler.

The supplied script ignores request arguments and returns `$stime($time())`: the current client-host time formatted as a local date/time string. It uses the host's local timezone and does not provide an independently verified clock or a separate timezone field. The formatting uses the active locale's `%c` date/time representation, normally resembling `Thu Oct 15 13:00:00 2026`.

Replies normally pass through [on ctcp_reply](../on/ctcp_reply.md) and display as `CTCP TIME reply from sender: date-and-time` unless scripts suppress or customize display. Request hooks or deactivating the registered CTCP can prevent responding. Automatic replies are subject to `no_ctcp_flood` reply throttling; outgoing requests are not throttled by that setting.

## Example

```text
/ctcp john time
```

Request the date and time from John's client.

## Source references

- [script/ctcp](../../script/ctcp): registration of the `time` request handler and description.
- [script/builtins](../../script/builtins): loads the CTCP script.
- [source/functions.c](../../source/functions.c): `function_time` and `function_stime` obtain and format the timestamp.
- [source/ircaux.c](../../source/ircaux.c): `my_ctime` formats local time.
- [source/commands.c](../../source/commands.c) and [source/ctcp.c](../../source/ctcp.c): request sending, reply hooks/display, and reply throttling.
