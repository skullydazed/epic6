# set flood_warning

## Removed setting

The former setting used this syntax:

```text
/set flood_warning [on|off|toggle]
```

When on, it displayed a warning identifying a sender and flood type when the former incoming flood detector activated. When off, it suppressed that built-in warning; scripts could still display their own messages through [on flood](on_flood.md).

EPIC6 removes that detector and hook, and does not register `flood_warning` as a built-in setting. There is no supported value, active default, or automatic flood-warning display controlled by this name.

Without script-provided handling of unknown settings, an obsolete assignment such as this follows the ordinary unknown-variable path:

```text
/set flood_warning off
```

It reports `No such variable "FLOOD_WARNING"`. Remove such assignments from startup scripts. A script accepting the name would define its own warning policy rather than restore the former C detector.

## Current scope

Scripts implementing incoming rate tracking through current message events must decide when and how to print their own warnings. Neither this setting nor an old `on flood` definition installs that tracking in EPIC6. See [on general_privmsg](../on/general_privmsg.md) and [on general_notice](../on/general_notice.md) for current message events.

This was a local incoming-detector display option, not a control for server-generated flood warnings or all client diagnostics. Outgoing CTCP reply throttling is also a separate path. There is no direct built-in replacement for the former warning toggle.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of the `flood` hook.
- [source/vars.c](../../source/vars.c): current registration omits this setting and handles unknown-variable errors.
- [source/hook.c](../../source/hook.c): current registration omits the former `flood` event.
- [source/parse.c](../../source/parse.c): incoming message paths use current hooks without the former flood detector or its warning path.
- [source/ctcp.c](../../source/ctcp.c): outgoing reply throttling is separate.
