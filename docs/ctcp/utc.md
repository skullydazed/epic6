# utc

## Synopsis

```text
/ctcp <target> utc <time-value>
```

## Description

Send a timestamp for the receiving client to render in its own local date/time representation. `<time-value>` is an integer number of seconds since the Unix epoch, as returned by `$time()`. Unlike [ping](ping.md), it uses one seconds value rather than a seconds-and-microseconds pair.

This CTCP is intended to transform received message text locally, rather than ask for a network reply. Not all clients support that transformation. The [ctcp](../commands/ctcp.md) command sends the payload to a nickname or channel; `*` selects the current channel. Delivery and filtering depend on the IRC server and destination. There are no `utc`-specific options.

## EPIC6 behavior

The conversion is supplied by [script/ctcp](../../script/ctcp), loaded by [script/builtins](../../script/builtins), rather than a C built-in `utc` handler. A setup that does not load it needs `/load ctcp`. Both the request and response handlers return `$stime(<arguments>)`; neither sends an automatic reply.

The timestamp is formatted using the receiving host's local timezone and the active locale's `%c` date/time representation. The supplied script registers a whole-message replacement, not an argument replacement. In the current C parser, a nonempty result replaces the text before and including that CTCP; text after the CTCP is appended again. A preceding label such as `Away since ` can therefore be lost. Use the CTCP as the whole message when you need predictable output.

The handler is not marked special, so the generic [on ctcp](../on/ctcp.md) or [on ctcp_reply](../on/ctcp_reply.md) event and its normal diagnostic can also occur. Request hooks, CTCP activation, and other scripts can alter processing.

## Invalid values

A missing timestamp or the value `-1` makes `$stime()` return an empty string, so no replacement is made. Values outside the platform's supported time range, or rejected by its local-time conversion routine, produce `value <invalid>`. Supported date ranges depend on the platform's time representation. Supply a valid integer timestamp rather than relying on malformed-input conversion.

## Example

```text
/ctcp john utc $time()
```

Send the current timestamp to John. With the supplied handler loaded, John's EPIC6 client renders it in John's local timezone and locale. The result does not establish that either client's clock is accurate.

## Source references

- [script/ctcp](../../script/ctcp): request and response handlers return the formatted timestamp.
- [script/builtins](../../script/builtins): loads the CTCP script.
- [source/ctcp.c](../../source/ctcp.c): `split_CTCP` and `do_ctcp` implement whole-message replacement and generic hook/display processing.
- [source/functions.c](../../source/functions.c): `function_stime` parses the time argument and handles missing values and `-1`.
- [source/ircaux.c](../../source/ircaux.c): `my_ctime` checks the platform range, converts local time, and formats `%c`.
