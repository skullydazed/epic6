# set flood_after

## Removed setting

The former setting used this syntax:

```text
/set flood_after [<number>]
```

It selected how many incoming messages had to be received before the former [on flood](on_flood.md) detector could trigger. Detection also depended on the message rate reaching the separate `flood_rate` threshold.

EPIC6 removes that incoming flood-control path and does not register `flood_after` as a built-in setting. There is no supported count, default, or automatic `flood` event controlled by this name now.

Without script-provided handling of unknown settings, an obsolete assignment such as this follows the ordinary unknown-variable path:

```text
/set flood_after 5
```

It reports `No such variable "FLOOD_AFTER"`. Remove such assignments from startup scripts. A script accepting the name would implement its own counting and policy rather than enable the former built-in detector.

## Current scope

Scripts needing incoming rate tracking can count relevant events, such as [on general_privmsg](../on/general_privmsg.md) and [on general_notice](../on/general_notice.md), and choose their own time windows and actions. Those events do not automatically reproduce the removed detector's thresholds.

The separate CTCP reply-throttling code concerns outgoing automatic replies, not this incoming-message count. It is not a replacement `flood_after` setting. Server-side rate limits and flood protection are also independent of this removed local setting.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of the `flood` hook.
- [source/vars.c](../../source/vars.c): current variable registration omits `flood_after` and handles unknown-variable errors.
- [source/hook.c](../../source/hook.c): current event registration omits the former `flood` event.
- [source/parse.c](../../source/parse.c): incoming message paths invoke current message events without the former flood detector.
- [source/ctcp.c](../../source/ctcp.c): outgoing CTCP reply throttling is a separate path.
