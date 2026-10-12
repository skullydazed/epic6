# idle function

## Synopsis

`$idle()`

## Technical

Returns the client's idle time in whole seconds, using the same implementation as the `$E` expando. Arguments, if any, are ignored. The function is provided for compatibility with ircII.

Normally this measures time since your last keypress: EPIC resets its global idle timestamp when handling input from any screen's input descriptor. The reset happens before key decoding or command execution, so you do not need to submit a line. In non-fullscreen mode, input is handled as complete lines instead. The timestamp is initialized when EPIC enters its main processing loop.

Incoming IRC messages and commands run by timers or scripts do not themselves reset this timestamp. In particular, sending a message programmatically does not count as a keypress. This is one client-wide value, independent of the selected server or window.

## Practical

Use `$idle()` instead of `$E` when compatibility with standard ircII matters. Client idle time differs from the idle time reported by an IRC server, whose definition depends on the server and commonly relates to sent messages.

The [on](../commands/on.md) `idle` event uses the same timestamp but supplies whole minutes and is checked by the client's minute clock update, rather than returning seconds on demand.

## Returns

An integer string containing current wall-clock seconds minus the recorded activity timestamp's seconds. Subsecond precision is discarded. This uses the real-time clock rather than a monotonic clock, so clock adjustments can make the result jump or become negative. There is no separate error result or diagnostic.

## Examples

```irc
@ idle_seconds = idle()
```

This stores the current client idle time. Calling the function does not reset it.

## History

First appeared in ircII-2.8.7.

## Source

Registration, `$E`, and the shared elapsed-seconds calculation: [source/functions.c](../../source/functions.c). Input resets: [source/screen.c](../../source/screen.c). Startup initialization: [source/irc.c](../../source/irc.c). Real-time clock acquisition: [source/ircaux.c](../../source/ircaux.c). Minute-based idle events: [source/clock.c](../../source/clock.c).
