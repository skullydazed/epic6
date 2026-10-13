# $sequence_point()

## Synopsis

`$sequence_point()`

## Technical

Returns EPIC's current event-loop entry counter as a decimal integer. The process-wide counter starts at zero and is incremented at the beginning of every call to the client's `io()` event loop, including nested calls.

The function only reads the counter. It does not evaluate its input, increment the counter, or force an event-loop pass. Arguments are ignored by the C handler, although normal EPIC function-argument expansion still happens before it runs.

## Practical

Use this as a debugging marker to compare when script code runs relative to event-loop entries. Consecutive calls without intervening event processing return the same value. Commands such as [/pause](../commands/pause.md) and [/wait](../commands/wait.md) can reenter the event loop and therefore change it while a script is waiting.

This is not elapsed time, a count of script statements, or a count of IRC messages. One event-loop entry can process multiple events, and nested entries also count. Values belong to the current client process and restart with a new process.

## Returns

The current counter, formatted as a signed decimal integer. There are no options or special error results. No IRC connection is required.

Reading the function does not require [/xdebug sequence_points](../commands/xdebug.md). That debug flag separately enables sequence-point status display and status refreshes; it does not control whether the counter increments.

## Examples

`$sequence_point()` returns the current value. `$sequence_point(ignored)` returns the same value if no event-loop entry occurs between the calls.

Registration and `function_sequence_point()`: [source/functions.c](../../source/functions.c). Counter initialization and increments in `io()`: [source/irc.c](../../source/irc.c). Debug flag: [source/debug.c](../../source/debug.c). Status display: [source/status.c](../../source/status.c).
