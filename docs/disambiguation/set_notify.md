# set notify

## Removed setting

The former setting used this syntax:

```text
/set notify [on|off|toggle]
```

It enabled or disabled periodic polling of connected servers for nicknames on the built-in notify list. Polling used the former `notify_interval` setting; status changes invoked [notify_signon](on_notify_signon.md) or [notify_signoff](on_notify_signoff.md) hooks.

EPIC6 retires that built-in polling system and does not register `notify` as a setting. There is no supported boolean value, default, or polling timer controlled by this name. Without script-provided handling of unknown settings, `/set notify off` reports `No such variable "NOTIFY"`. Remove obsolete assignments from startup scripts. A script accepting this name supplies its own behavior.

## Current notify behavior

The supplied [script/notify](../../script/notify), loaded by `builtins`, defines the [notify](../commands/notify.md) command as an alias around the server's MONITOR facility. It does not consult `set notify` or periodically poll according to `notify_interval`. Online and offline numeric replies `730` and `731` are displayed directly by script hooks rather than the removed notify hooks.

On a server advertising MONITOR support, add a nickname to the watch list with:

```text
/notify alice
```

Remove it with `/notify -alice`, request status with `/notify`, or clear the server's watch list with `/notify -`. Send additions and removals in separate commands: the supplied alias chooses the additions branch when both are present. The server controls support, list limits, and notification timing. Without advertised support, the alias reports that MONITOR is unavailable.

`/set notify off` does not disable these server notifications. Use the alias's removal or clear operations instead. This setting is also unrelated to [notify_level](../set/notify_level.md), which concerns hidden-window activity notifications.

## History

The former setting first appeared in EPIC4-1.1.13.

## Source references

- [source/vars.c](../../source/vars.c): current setting registration omits `notify` and handles unknown-variable errors.
- [KNOWNBUGS](../../KNOWNBUGS): records retirement of built-in notify and introduction of the MONITOR script.
- [script/notify](../../script/notify): server-dependent MONITOR alias and numeric handlers.
- [script/builtins](../../script/builtins): loads the supplied notify script.
