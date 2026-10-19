# set key_interval

## Synopsis

```text
/set key_interval [<milliseconds>]
```

Default: `1000` milliseconds (one second). With no value, [set](../commands/set.md) displays the current interval. Negative or nonnumeric values are rejected. Non-negative values below `10` produce `Setting KEY_INTERVAL below 10ms is not recommended.` and are changed to `10`, not accepted as zero or a disabled timeout.

## Description

Sets the timeout for resolving an ambiguous key binding: a key sequence that has its own action but is also the prefix of a longer sequence. For example, Escape can have a standalone action while also beginning a function key's escape sequence.

When EPIC reaches such a binding, it waits for more input and schedules a timeout check. If the shorter sequence is still pending after the interval, EPIC executes its action and resets sequence matching. If additional characters complete a longer binding first, that binding can run instead.

A prefix with no action of its own is not resolved to an action by this timeout; it remains a prefix awaiting more input. Unambiguous completed bindings execute without this wait. The setting is therefore not a general delay applied to every keystroke.

## Choosing an interval

A shorter interval makes a standalone prefix key respond sooner, but can split slowly arriving terminal escape sequences into unintended actions. A longer interval tolerates more delay between characters but makes standalone ambiguous keys feel less responsive. The appropriate value depends on terminal behavior and connection latency.

Timeouts are processed through the client's event loop, so the action need not execute at the exact deadline. Existing timeout callbacks check the current setting; changing the value does not immediately reschedule all pending ambiguous sequences. The interval is global and sends no IRC request.

## Example

Create an Escape action that can conflict with longer Escape-prefixed bindings, then select a quarter-second interval:

```text
/bind ^[ parse_command {echo You pressed Escape!;}
/set key_interval 250
```

The ambiguity exists only when longer bindings beginning with Escape are installed. Increase the interval if those terminal keys stop resolving reliably. Restore the compiled default with `/set key_interval 1000`.

## History

This setting first appeared in EPIC4-1.1.7.

## Source references

- [source/keys.c](../../source/keys.c): key dispatch schedules checks for nodes with both a binding and child map; `timeout_keypress` resolves a pending bound node after the interval; `set_key_interval` enforces the 10ms minimum.
- [source/vars.c](../../source/vars.c): integer registration and generic validation.
- [include/config.h](../../include/config.h): default 1000ms.
