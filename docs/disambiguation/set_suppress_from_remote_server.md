# set suppress_from_remote_server

## Removed setting

The former setting used this syntax:

```text
/set suppress_from_remote_server [on|off|toggle]
```

It controlled whether default displays of remote server replies included the replying server's name, for queries such as [stats](../commands/stats.md) or [whois](../commands/whois.md). Turning it on suppressed that extra attribution without disabling reply hooks.

EPIC6 does not register `suppress_from_remote_server` as a setting, and its numeric display code does not read it. There is no supported boolean value or suppression policy controlled by this name. An unused historical default macro does not provide an active setting.

Without script-provided handling of unknown settings, `/set suppress_from_remote_server on` reports `No such variable "SUPPRESS_FROM_REMOTE_SERVER"`. Remove obsolete assignments from startup scripts. A script accepting this name would supply its own display behavior.

## Current reply display

The generic numeric display helper appends `(from <sender>)` when it treats a reply as coming from another server. It omits that attribution when the sender begins with the connected server's reported name, compared case-insensitively over the reported name's length. This is the implemented prefix comparison rather than an exact-name test.

That helper is not used for every numeric: reply-specific handlers and scripts can format replies differently. Numeric hooks remain available independently of this removed setting. To customize attribution, handle the relevant replies through [on](../commands/on.md) and replace their default display. The server determines the reply sender and contents; changing local formatting does not change the query or the server's response.

## Source references

- [source/vars.c](../../source/vars.c): current registration omits this name and handles unknown-variable errors.
- [source/numbers.c](../../source/numbers.c): `display_msg` compares the sender and appends remote attribution without consulting a setting; numeric hooks precede applicable default displays.
- [include/config.h](../../include/config.h): retains the unused historical default macro.
