# set flood_maskuser

## Removed setting

The former setting used this syntax:

```text
/set flood_maskuser <number>
```

It belonged to the former incoming flood-control facility and determined which senders were grouped as one flooder. Its documented modes included treating each distinct `user@host` separately (`0`) and grouping all users on a host together (`2`). These describe the former interface, not supported EPIC6 modes.

EPIC6 removes the incoming flood detector and does not register `flood_maskuser` as a built-in setting. No numeric value selects a grouping rule now, and there is no active default or sender-grouping behavior attached to this name. See the removed [on flood](on_flood.md) event and [flood_after](set_flood_after.md) threshold.

Without script-provided handling of unknown settings, an obsolete assignment such as this follows the ordinary unknown-variable path:

```text
/set flood_maskuser 0
```

It reports `No such variable "FLOOD_MASKUSER"`. Remove such assignments from startup scripts. A script accepting the name would implement its own grouping and detection policy rather than enable the former C detector.

## Current scope

Scripts implementing rate tracking through current message events must choose their own sender identity and grouping rules. Grouping by a server-reported host can combine several people sharing a host; grouping by individual identity requires the script to account for identity changes. Neither policy is automatically installed by this removed setting.

This setting does not configure server-side flood protection or the separate outgoing CTCP reply throttle.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of the `flood` hook.
- [source/vars.c](../../source/vars.c): current registration omits this setting and handles unknown-variable errors.
- [source/hook.c](../../source/hook.c): current event registration omits `flood`.
- [source/parse.c](../../source/parse.c): incoming message paths use current message hooks without the former sender-grouping detector.
- [source/ctcp.c](../../source/ctcp.c): outgoing reply throttling is separate from incoming sender grouping.
