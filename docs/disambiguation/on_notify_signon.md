# on notify_signon (removed)

## Former synopsis

```text
/on [<noise>]notify_signon "<pattern>" { <commands> }
```

## Description

The `notify_signon` event formerly ran when the client's internal notify list detected that a watched nickname had signed on to IRC. EPIC6 has removed this named hook and the former internal notify-list event path. It no longer generates this event or its historical fields.

## Former parameters

| Parameter | Former meaning |
| --- | --- |
| `$0` | Nickname detected online. |
| `$1` | Person's `user@host`, when the former `notify_userhost_automatic` setting was on. |

These describe the removed interface, not arguments supplied by EPIC6. The former automatic-userhost setting is not a current built-in setting controlling this event.

## Compatibility and current script behavior

Attempting to register `/on notify_signon ...` is rejected with `No such ON function: NOTIFY_SIGNON`. Retained references in `script/notify.jm`, including attempts to fetch userhosts and synthesize the event, do not restore the named hook.

The current `script/notify` provides [notify](../commands/notify.md) as a wrapper around server `monitor` requests. It directly handles online numeric `730`, echoing the server-provided fields, without emitting `notify_signon`. This facility requires server support for `monitor`; its replies describe nickname availability and can include initial status, not only newly observed connection establishment.

Scripts wanting to customize these notifications can handle the actual server numeric replies using [on](../commands/on.md). Those arguments are server reply fields rather than this removed hook's nickname and optional userhost contract.

For a user's join to a shared channel, see [on join](../on/join.md). Joining a channel and becoming available under a watched nickname are different observations; neither is a universal replacement for the other.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records removal of the previously sunset notify signon and signoff hooks.
- [source/hook.c](../../source/hook.c): the named-hook table omits `notify_signon`; `find_hook` rejects unknown event names.
- [include/hook.h](../../include/hook.h): current event identifiers contain no notify-signon hook.
- [source/vars.c](../../source/vars.c): current built-in settings omit `notify_userhost_automatic`.
- [script/notify](../../script/notify): defines the monitor wrapper and directly formats numeric `730` without emitting the former event.
- [script/notify.jm](../../script/notify.jm): retains historical userhost and hook references.
