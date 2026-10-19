# set no_ctcp_flood

## Removed setting

The former setting used this syntax:

```text
/set no_ctcp_flood [on|off|toggle]
```

It controlled protection against sending excessive CTCP replies in response to incoming requests. EPIC6 does not register `no_ctcp_flood` as a built-in setting and provides no supported default or boolean control under this name.

Without script-provided handling of unknown settings, `/set no_ctcp_flood on` reports `No such variable "NO_CTCP_FLOOD"`. Remove obsolete assignments from startup scripts. A script accepting the same name supplies its own behavior; it does not automatically connect that setting to the C reply throttle.

## Remaining CTCP code

The C `send_ctcp` routine still contains an outbound reply-throttling branch referencing the old `no_ctcp_flood` variable identifier. Its test uses a numeric built-in variable index, while this name is absent from the current registration table. The presence of that branch does not make the former setting available, and its effective condition should not be relied upon as a supported configuration interface.

The retained algorithm uses one shared timestamp, not a separate timestamp per nickname or server. When its condition is enabled, a reply attempted less than two whole clock seconds after the previous timestamp is dropped; dropping a reply updates the timestamp again. Repeated reply attempts can therefore extend the quiet period indefinitely. CTCP requests sent through this routine are not throttled by that branch. It drops outbound replies rather than preventing incoming request hooks from running.

These details do not establish that every CTCP reply is currently throttled: the missing registration leaves the condition without a supported setting. Do not rely on historical per-nickname or multiple-client limitations as a description of this retained algorithm.

The bundled `autoget` script also contains obsolete advice to turn this setting off. That reference does not restore the setting or the removed DCC transfer implementation.

## Source references

- [source/vars.c](../../source/vars.c): current registration omits the name; `get_int_var` accesses variables by numeric index rather than looking up a setting name.
- [include/vars.h](../../include/vars.h): retains the old variable identifier.
- [source/ctcp.c](../../source/ctcp.c): `send_ctcp` retains the global timestamp and outbound-reply throttle branch.
- [script/autoget](../../script/autoget): obsolete setting check and advice.
