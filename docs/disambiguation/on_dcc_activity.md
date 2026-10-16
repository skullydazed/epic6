# on dcc_activity (removed)

## Former synopsis

```text
/on [<noise>]dcc_activity "<pattern>" { <commands> }
```

## Description

The `dcc_activity` event formerly reported activity on a DCC file transfer, including [send](ctcp_send.md) and [get](ctcp_get.md). It ran when the client updated the `%D` status expando, which described the most recently active open DCC transaction.

EPIC6 has removed the DCC implementation and this named hook. There is no built-in DCC activity event, transfer refnum, or associated status-bar update to intercept. The former behavior of suppressing the event to reduce status-bar flicker is unavailable.

## Former parameter and default action

The event supplied the active DCC transaction's refnum in `$0`, for use with the former `dccctl` function. Allowing default handling updated the status bar. These describe the removed interface, not current EPIC6 behavior.

## Compatibility

Attempting to register `/on dcc_activity ...` is rejected with `No such ON function: DCC_ACTIVITY`. A retained reference in `script/builtins` does not restore the event or DCC transfers. Scripts that implement their own transfers must provide their own progress reporting; there is no built-in replacement event with these arguments.

## History

The former hook first appeared in EPIC4-1.1.14.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `dccctl`, and related functionality.
- [source/hook.c](../../source/hook.c): the named-hook table omits `dcc_activity`; `find_hook` rejects unknown event names.
- [include/hook.h](../../include/hook.h): current event identifiers contain no DCC activity hook.
- [source/status.c](../../source/status.c): the status expando table has no former DCC `%D` callback.
- [script/builtins](../../script/builtins): retains a reference to the removed event without providing an implementation.
