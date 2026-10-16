# ping

## Synopsis

```text
/ctcp <target> ping <seconds> <microseconds>
```

## Description

CTCP `ping` supplies the protocol support for the [ping](../commands/ping.md) command. Prefer `/ping <target>` for ordinary use: it supplies the current timestamp automatically. The timestamp consists of seconds since the Unix epoch and a separate microsecond fraction, not a single decimal number.

A supporting recipient echoes the supplied arguments in a CTCP reply. The requesting client can compare the returned timestamp with its current time to estimate round-trip delay, including server routing, remote client processing, and any throttling. This is not an IRC server `ping`/`pong` exchange.

`<target>` is normally a nickname. The [ctcp](../commands/ctcp.md) command also accepts a channel or `*` for the current channel, where multiple clients may respond. There are no `ping`-specific options. In ordinary use, the request is sent as an IRC `privmsg` and the reply as a CTCP `notice`. Delivery and filtering depend on the server and remote client; no reply is guaranteed.

## EPIC6 behavior

The C `/ping` command constructs the timestamp and invokes the generic CTCP sender. Automatic replies and elapsed-time interpretation are supplied by [script/ctcp](../../script/ctcp), loaded by [script/builtins](../../script/builtins). A setup that does not load it needs `/load ctcp` for those handlers.

The supplied request handler echoes the argument text without interpreting it. Its reply handler subtracts the first two returned timestamp fields from the current time, borrowing one second when needed, and substitutes `<elapsed> seconds` for the reply arguments. It temporarily enables floating-point arithmetic for this calculation and restores the prior setting afterward.

The reply then passes through [on ctcp_reply](../on/ctcp_reply.md) and normally displays as `CTCP PING reply from sender: elapsed seconds`. If the timestamp fields are absent or evaluate as false, or the computed delay is negative, the script returns no replacement and the original arguments remain available for normal reply display. Extra fields after the first two timestamp fields do not participate in timing.

The handler does not match the timestamp against a saved outstanding request, so the displayed delay is calculated from the received values rather than independently authenticated. Wall-clock changes can also affect the result. Automatic replies are subject to `no_ctcp_flood` reply throttling; outgoing requests are not throttled by that setting. Hooks or deactivating the registered CTCP can suppress responses.

## Examples

```text
/ping john
```

Send a timestamped request to John and, with the reply script loaded, display the calculated round-trip time when a suitable reply arrives.

For a script that needs the underlying request explicitly:

```text
/ctcp john ping $utime()
```

## Source references

- [source/commands.c](../../source/commands.c): `pingcmd` constructs the timestamp and calls `ctcpcmd`.
- [script/ctcp](../../script/ctcp): request echoing, `do_ping_reply`, response registration, and argument replacement.
- [script/builtins](../../script/builtins): loads the CTCP script.
- [source/functions.c](../../source/functions.c): `function_utime` returns seconds and microseconds.
- [source/ctcp.c](../../source/ctcp.c): sending, reply dispatch, argument replacement, hooks, and throttling.
