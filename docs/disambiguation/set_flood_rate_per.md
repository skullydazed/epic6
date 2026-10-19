# set flood_rate_per

## Removed setting

The former setting used this syntax:

```text
/set flood_rate_per [<number>]
```

It supplied the denominator of the former incoming-message flood rate limit; [flood_rate](set_flood_rate.md) supplied the numerator. Its historical default was `1`. It was part of the same facility as [on flood](on_flood.md).

EPIC6 removes that incoming flood detector and does not register `flood_rate_per` as a built-in setting. There is no supported denominator, active default, or counting interval controlled by this name now.

Without script-provided handling of unknown settings, an obsolete assignment such as this follows the ordinary unknown-variable path:

```text
/set flood_rate_per 1
```

It reports `No such variable "FLOOD_RATE_PER"`. Remove such assignments from startup scripts. A script accepting the name would provide its own calculation and policy rather than restore the former C detector.

## Current scope

Scripts needing incoming rate tracking can use current message events, such as [on general_privmsg](../on/general_privmsg.md) and [on general_notice](../on/general_notice.md), but must define their own time window, count, sender grouping, and response. This obsolete setting does not install any of those rules.

Server-side flood protection and outgoing CTCP automatic-reply throttling are separate mechanisms. They do not acquire a denominator from this removed setting, and there is no direct built-in replacement for the former detector's rate calculation.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of the `flood` hook.
- [source/vars.c](../../source/vars.c): current registration omits this setting and handles unknown-variable errors.
- [source/hook.c](../../source/hook.c): current event registration omits `flood`.
- [source/parse.c](../../source/parse.c): incoming messages use current hooks without the former flood detector.
- [source/ctcp.c](../../source/ctcp.c): outgoing reply throttling is separate.
