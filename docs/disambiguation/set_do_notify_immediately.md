# set do_notify_immediately

## Removed setting

The former setting used this syntax:

```text
/set do_notify_immediately [on|off|toggle]
```

When on, it checked the former built-in notify list after each `notify` command rather than waiting for its regular minute-based check. Disabling immediate checks was intended to reduce work when large notify lists were restored during server connection.

EPIC6 retires that built-in notify implementation and does not register `do_notify_immediately` as a setting. There is no supported value, default, or immediate-versus-minute polling policy controlled by this name. Its remaining enum identifier does not provide active behavior.

Without script-provided handling of unknown settings, an obsolete assignment such as this follows the ordinary unknown-variable path:

```text
/set do_notify_immediately on
```

It reports `No such variable "DO_NOTIFY_IMMEDIATELY"`. Remove such assignments from startup scripts. A script accepting the name would supply its own policy rather than enable the former built-in checks.

## Current notify behavior

The supplied [script/notify](../../script/notify) defines a `notify` alias around server `monitor` requests. It does not read this setting or implement a minute-based polling alternative. With a server advertising MONITOR support, the alias requests status, additions, removals, or clearing through that protocol; response timing and limits depend on the server. It reports an unsupported-server message when MONITOR is unavailable.

For example, with that script loaded and a supporting server:

```text
/notify alice
```

This asks the script to add Alice through MONITOR; `do_notify_immediately` does not affect the request or its response. Numeric `730` and `731` handlers in the script display online and offline information directly.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of built-in notify functionality.
- [source/vars.c](../../source/vars.c): current variable registration omits the setting and handles unknown-variable errors.
- [include/vars.h](../../include/vars.h): retains the historical identifier without registration.
- [script/notify](../../script/notify): script-provided MONITOR wrapper and status displays.
