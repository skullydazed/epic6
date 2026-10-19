# set flood_ignore

## Removed setting

The former flood-control setting used this syntax:

```text
/set flood_ignore [on|off]
```

When on, it prevented processing of messages classified as part of a flood by the former incoming flood detector. When off, those messages continued through normal processing.

EPIC6 removes that detector and does not register `flood_ignore` as a built-in setting. There is no supported value, default, or automatic message-rejection policy controlled by this name. See the removed [on flood](on_flood.md) event and [flood_after](set_flood_after.md) threshold.

Without script-provided handling of unknown settings, an obsolete assignment such as this follows the ordinary unknown-variable path:

```text
/set flood_ignore on
```

It reports `No such variable "FLOOD_IGNORE"`. Remove such assignments from startup scripts. A script accepting the name would implement its own detection and suppression policy rather than activate the removed C path.

## Current scope

Current message hooks, such as [on general_privmsg](../on/general_privmsg.md) and [on general_notice](../on/general_notice.md), can support script-provided rate tracking and display decisions. Suppression in those hooks has its own documented scope; it should not be assumed to reproduce the former policy of skipping message processing altogether.

The separate outgoing CTCP reply throttle is not this incoming-message filter. Likewise, server-side flood protection is independent of this removed setting. There is no direct built-in replacement switch that classifies incoming floods and ignores them through the former detector.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of the `flood` hook.
- [source/vars.c](../../source/vars.c): current registration omits `flood_ignore` and handles unknown-variable errors.
- [source/hook.c](../../source/hook.c): current registration omits the former `flood` event.
- [source/parse.c](../../source/parse.c): current incoming message handlers use message events without the former flood detector.
- [source/ctcp.c](../../source/ctcp.c): outgoing automatic-reply throttling is separate.
