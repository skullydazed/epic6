# timerctl

## Synopsis

```irc
$timerctl(REFNUMS)
$timerctl(REFNUM <refnum>)
$timerctl(DELETE <refnum>)
$timerctl(GET <refnum> <item>)
$timerctl(SET <refnum> <item> <value>)
```

## Technical

A low-level interface to pending timers. Timer names are matched case-insensitively and exactly. Operation and item names accept case-insensitive prefixes, but use full names: `REFNUM` is checked before `REFNUMS`, so abbreviated forms select the former.

Arguments use function-word parsing. String setters consume the remaining raw text, including quotation marks. Numeric setters use base-zero integer parsing, ignore trailing nonnumeric text, and can convert nonnumeric input to zero. Missing arguments or unknown operations/items generally return empty.

| Operation | Return and effect |
| --- | --- |
| `REFNUMS` | List all pending timer names, including internal C callback timers. The order is the pending queue order. |
| `REFNUM <refnum>` | Return the stored name if pending, otherwise empty. |
| `ADD ...` | Not implemented; returns empty. Create timers with the [`timer`](../commands/timer.md) command. |
| `DELETE <refnum>` | Delete a pending command timer and return `0`; return empty if absent or an internal C callback timer. |
| `GET <refnum> <item>` | Return the item below, or empty if unavailable. |
| `SET <refnum> <item> <value>` | Modify a command timer; always return empty, including on success. Internal C callback timers cannot be modified. |

## Get and set items

| Item | Value and effect |
| --- | --- |
| `TIMEOUT` | Absolute expiration timestamp as two integers: seconds and microseconds, like [`utime`](utime.md). Setting requires both integers. |
| `INTERVAL` | Repeat interval as seconds and microseconds; setting requires both integers and affects later repeat scheduling, not the current deadline. |
| `COMMAND` | Command body to execute. Get returns empty for C callback timers; set replaces the raw body, including an empty body. |
| `SUBARGS` | Argument text supplied as `$*` when the body runs; set replaces raw text. Get returns empty for C callback timers. |
| `REPEATS` | Remaining scheduled executions, including the pending occurrence. Positive counts are finite; `-1` repeats indefinitely. |
| `SERVER` | Server reference for a server timer, otherwise `-1`. Setting changes its domain to server, replacing any window domain. |
| `WINDOW` | Window reference for a window timer, otherwise `-1`. Setting changes its domain to window, replacing any server domain. |
| `PACKAGE` | Package label; get returns empty if none, and set replaces raw text. Package-based [`unload`](../commands/unload.md) can remove it. |

A timer for which both `SERVER` and `WINDOW` return `-1` is normally a general timer. No setter selects the general domain explicitly. Domain setters do not validate the reference immediately; execution handles missing domains according to the timer's existing cancellation policy.

`CANCELABLE` and `FIRES` are unsupported. `GET` without an item does not list properties.

## Restrictions

Setters bypass normal timer creation validation. Microseconds are multiplied by 1000 without range normalization or overflow checks; use values from `0` through `999999`. Use positive repeat counts or `-1`: zero and other negative values do not cancel a timer and can produce unintended repeated execution. A zero or negative repeat interval can keep a repeated timer due and prevent normal progress.

Changing `TIMEOUT` does not reinsert or sort the timer in the pending queue. The scheduler examines the queue's first timer, so this can delay other due timers or leave the changed timer behind a later deadline. Prefer the timer command's update facilities for rescheduling.

The currently executing timer has already been removed from the pending queue. A repeating timer's next occurrence is cloned and scheduled before its body runs; lookup or mutation using the same name therefore addresses that pending occurrence, not the body currently executing. Deleting it prevents subsequent executions but does not stop the current body.

## Examples

```irc
$timerctl(REFNUMS)                  List pending timer names.
$timerctl(GET example COMMAND)      Read a pending command timer's body.
@ timerctl(SET example REPEATS 1)    Leave one pending execution.
@ timerctl(SET example SUBARGS hello world)
@ timerctl(DELETE example)          Delete the pending command timer.
```

The examples assume a command timer named `example` already exists. Read values back to check setters, since their empty return does not indicate success. No server connection is required to inspect timers.

## History

First appeared in EPIC4-1.1.8.

## Source

Wrapper: [`source/functions.c`](../../source/functions.c:7975). Interface: [`source/timer.c`](../../source/timer.c:1182). Queue lookup and execution: [`get_timer`](../../source/timer.c:632), [`ExecuteTimers`](../../source/timer.c:1075).
