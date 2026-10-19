# set flood_users

## Removed setting

The former setting used this syntax:

```text
/set flood_users [<number>]
```

It limited how many users the former incoming flood-protection facility tracked. Increasing the limit allowed more senders to be tracked at the cost of additional detector work. This was a local tracking limit, not a limit on channel membership or the number of people allowed to message the client.

EPIC6 removes that detector and does not register `flood_users` as a built-in setting. There is no supported limit, active default, or built-in flood-tracking capacity controlled by this name. See the removed [on flood](on_flood.md) event and [flood_after](set_flood_after.md) threshold.

Without script-provided handling of unknown settings, an obsolete assignment such as this follows the ordinary unknown-variable path:

```text
/set flood_users 100
```

It reports `No such variable "FLOOD_USERS"`. Remove such assignments from startup scripts. A script accepting the same name would define its own tracking limits rather than restore the former C detector.

## Current scope

Scripts can implement rate tracking through current events such as [on general_privmsg](../on/general_privmsg.md) and [on general_notice](../on/general_notice.md). Such scripts must choose their own identities, storage limits, expiry rules, and detection policy; this setting installs none of them.

The former performance-tuning advice does not apply to a nonexistent EPIC6 detector. Server-side flood protection and outgoing CTCP reply throttling are separate mechanisms, not consumers of this setting.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of the `flood` hook.
- [source/vars.c](../../source/vars.c): current registration omits `flood_users` and handles unknown-variable errors.
- [source/hook.c](../../source/hook.c): current event registration omits `flood`.
- [source/parse.c](../../source/parse.c): incoming messages use current hooks without the former tracking detector.
- [source/ctcp.c](../../source/ctcp.c): outgoing automatic-reply throttling is separate.
