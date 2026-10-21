# Window

For managing windows interactively, see the [window command](../commands/window.md):

```text
/window [window-reference] operation [arguments ...] ...
```

For programmatic queries and changes, see [windowctl](../functions/windowctl.md). For the event fired when output reaches a window, see [on window](../on/window.md). These are distinct interfaces.

## Servers and screens

Windows hold output and associate it with servers, channels, query targets, and levels. Several windows can share one server connection. A screen is the terminal/input surface containing visible windows; hidden windows have no screen but can still receive routed output and retain history. Hiding is different from destroying a window.

A window associated with a server in reconnect state can initiate its connection. Losing the last associated window can close the connection when the server's autoclose policy is enabled; this is not an unconditional rule for every server. See [server states](../special/server_state.md).

## Operation guide

| Purpose | Starting points |
| --- | --- |
| Server association | [server](../window/server.md), [noserv](../window/noserv.md). |
| Message routing and targets | [channel](../window/channel.md), [query](../window/query.md), [add](../window/add.md), [remove](../window/remove.md), [level](../window/level.md), [rejoin](../window/rejoin.md), [echo](../window/echo.md). |
| Placement and selection | [new](../window/new.md), [new_hide](../window/new_hide.md), [hide](../window/hide.md), [show](../window/show.md), [swap](../window/swap.md), [refnum](../window/refnum.md), [refnum_or_swap](../window/refnum_or_swap.md), [balance](../window/balance.md), [size](../window/size.md). |
| Views and held output | [clear](../window/clear.md), [flush](../window/flush.md), [hold_mode](../window/hold_mode.md), [scroll_backward](../window/scroll_backward.md), [scroll_forward](../window/scroll_forward.md), [scroll_end](../window/scroll_end.md), [scrollback](../window/scrollback.md). |
| Status and activity | [double](../window/double.md), [status_format](../window/status_format.md), [status_special](../window/status_special.md), [notify](../window/notify.md), [notify_level](../window/notify_level.md). |
| Retention, identity, and lifetime | [lastlog](../window/lastlog.md), [lastlog_level](../window/lastlog_level.md), [log](../window/log.md), [logfile](../window/logfile.md), [name](../window/name.md), [number](../window/number.md), [describe](../window/describe.md), [list](../window/list.md), [kill](../window/kill.md). |

Single-row and double-row status formats are separate: `status_format1` supplies the top row and `status_format2` the bottom row in double mode. Notification flags, activity messages, and custom activity formatting also have separate rules; see [status lines](../special/status_line.md) and [window activity handling](../special/window_activity_handling.md).

## Chaining operations

The initial target is the current **window**, not the server. A reference selects the target of subsequent operations without necessarily making it the input window. Each handler can change the target passed to the next operation. Invalid operations or references and handler failures stop further processing, but not every diagnostic is a failing return. Earlier changes are not rolled back.

Use full operation names: the dispatcher accepts prefixes in table order rather than universally rejecting ambiguous abbreviations. Each operation consumes its own arguments, so quote a string when that operation requires a single argument containing spaces. The command reference documents supported operations and current limitations; do not infer support from historical operation names alone.

## History and source

The window command existed in ircII2.1.5g; many operations are EPIC extensions.

- `source/window.c`: operation registry, dispatcher, window/server associations, and lifecycle.
- `source/status.c`: status-row selection and formatting.
- `source/screen.c`: window output routing.
- `source/functions.c`: `windowctl` function interface.
