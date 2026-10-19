# set notify_userhost_automatic

## Removed setting

The former setting used this syntax:

```text
/set notify_userhost_automatic [on|off|toggle]
```

It enabled automatic userhost lookups when the built-in notify system detected a nickname signing on, making user@host information available to [notify_signon](on_notify_signon.md) handlers. Previously known host information could avoid another server query.

EPIC6 retires that built-in notify system and does not register `notify_userhost_automatic` as a setting. There is no supported built-in value, default, automatic lookup policy, or cached-host optimization controlled by this name. Without script-provided handling of unknown settings, `/set notify_userhost_automatic on` reports `No such variable "NOTIFY_USERHOST_AUTOMATIC"`. Remove obsolete assignments from startup scripts.

## Current notify behavior

The [notify](../commands/notify.md) alias in [script/notify](../../script/notify), loaded by `builtins`, wraps server MONITOR requests. It displays online and offline numeric replies directly and does not read this setting, issue these automatic userhost queries, or invoke the retired signon hook. Information in a MONITOR reply comes from the server; enabling this removed setting cannot change it.

## Older optional script

The separate [script/notify.jm](../../script/notify.jm) contains its own variable and `on set` handler with this name. It initializes the script variable to `on`, accepts `on` or `off` assignments, and reports an error for other values, including `toggle`. Its online-detection branch issues `userhost -cmd` requests in groups of up to five nicknames when enabled. It then attempts to generate a script signon event with the nickname and user@host; that older script uses `$1` for the combined userhost value, not `$2`.

This is separate script logic, not an active C setting or behavior of the current MONITOR wrapper. The presence of the older handler does not establish that the whole script works with EPIC6's retired notify hooks. Do not assume its parameters or caching behavior match the former built-in implementation.

## Source references

- [source/vars.c](../../source/vars.c): current setting registration omits this name and handles unknown-variable errors.
- [include/vars.h](../../include/vars.h): retains the historical identifier without registering the setting.
- [script/notify](../../script/notify) and [script/builtins](../../script/builtins): current MONITOR implementation and loading.
- [script/notify.jm](../../script/notify.jm): separate script variable, assignment handler, and online userhost requests.
- [KNOWNBUGS](../../KNOWNBUGS): records retirement of built-in notify and its signon/signoff hooks.
