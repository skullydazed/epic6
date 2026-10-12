# getserial function

## Synopsis

`$getserial(hook + <number>)`

`$getserial(hook - <number>)`

## Description

Finds a serial number not currently used by any [on](../commands/on.md) hook of any event type. `hook` is the only supported type and is case-insensitive. The direction must be exactly `+` or `-`.

With `+`, the search starts at `number` and moves upward; with `-`, it starts there and moves downward. The starting number itself is eligible if unused. Zero is special: it starts the upward search at `1` or the downward search at `-1`, so serial zero is not selected when starting from zero.

The starting number is required. Omitting it returns the empty string; supply `0` explicitly for the usual positive or negative search. Integer conversion accepts decimal, `0x` hexadecimal, and leading-zero octal notation. Nonnumeric text converts to zero, and additional arguments are ignored. Supply a valid integer within `-32767` through `32767`.

This function searches existing hooks rather than maintaining a counter. It does not install a hook or reserve the returned serial. Repeated calls with unchanged hooks and arguments return the same number, and another script can claim it before you define your hook. The number is unused across all event types only at the time of a successful search.

This is related to specifying a bare `+` or `-` serial in `/on`. That command searches only the selected event type; `$getserial()` checks all event types.

## Returns

An unused serial as an integer string. Missing required arguments, an unsupported type, or an invalid direction returns empty without a diagnostic.

The search's upper limit is `32767` for `+` and lower limit is `-32767` for `-`. If every candidate is occupied, it returns `32768` or `-32768` respectively without checking that number. A starting number already beyond the limit in the search direction is also returned unchecked. Do not treat a result outside `-32767` through `32767` as a verified unused serial.

## Examples

```irc
@ positive_serial = getserial(hook + 0)
@ negative_serial = getserial(hook - 0)
```

These return `1` and `-1` respectively if those serials are unused across all hook types. `$getserial(hook + 500)` returns `500` if unused, otherwise the next available number up to the search limit.

## History

Written by wd; first appeared in EPIC4-1.1.8.

## Source

Registration, type and direction checks, and argument conversion: [source/functions.c](../../source/functions.c) and [include/functions.h](../../include/functions.h). The inclusive search, bounds, and `/on` serial selection: [source/hook.c](../../source/hook.c).
