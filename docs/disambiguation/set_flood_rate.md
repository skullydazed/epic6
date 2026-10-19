# set flood_rate

## Removed setting

The former setting used this syntax:

```text
/set flood_rate [<number>]
```

It supplied the numerator of the former incoming-message flood rate limit, with `flood_rate_per` supplying the denominator. Its historical default was `10`. It belonged to the same detector as [flood_after](set_flood_after.md) and [on flood](on_flood.md).

EPIC6 removes that detector and does not register `flood_rate` as a built-in setting. There is no active threshold, supported value, or current default attached to this name. The historical value does not configure incoming-message rate tracking in EPIC6.

Without script-provided handling of unknown settings, an obsolete assignment such as this follows the ordinary unknown-variable path:

```text
/set flood_rate 10
```

It reports `No such variable "FLOOD_RATE"`. Remove such assignments from startup scripts. A script accepting this name would supply its own rate calculation and policy rather than activate the removed C implementation.

## Current scope

Scripts can track current message events, such as [on general_privmsg](../on/general_privmsg.md) and [on general_notice](../on/general_notice.md), but must choose their own counting interval, thresholds, and actions. No equivalent threshold is automatically installed by this removed setting.

This setting is unrelated to server-enforced flood limits or the separate outgoing CTCP automatic-reply throttle. Changing an obsolete local assignment cannot change those policies.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of the `flood` hook.
- [source/vars.c](../../source/vars.c): current registration omits `flood_rate` and handles unknown-variable errors.
- [source/hook.c](../../source/hook.c): current event registration omits `flood`.
- [source/parse.c](../../source/parse.c): incoming message paths use current hooks without the former detector.
- [source/ctcp.c](../../source/ctcp.c): outgoing CTCP reply throttling is separate.
