# on notify_signoff (removed)

## Former synopsis

```text
/on [<noise>]notify_signoff "<pattern>" { <commands> }
```

## Description

The `notify_signoff` event formerly ran when the client's internal notify list detected that a watched nickname had signed off IRC. It supplied that nickname in `$0`.

EPIC6 has removed this named hook and the former internal notify-list event path. It does not generate a `notify_signoff` event or provide its historical single-nickname argument.

## Compatibility and current script behavior

Attempting to register `/on notify_signoff ...` is rejected with `No such ON function: NOTIFY_SIGNOFF`. Retained references in `script/notify.jm`, including attempts to synthesize the event, do not restore its named-hook registration.

The current `script/notify` provides [notify](../commands/notify.md) as a wrapper around server `monitor` requests. It handles offline numeric `731` directly with an echo of the server-provided fields and does not emit `notify_signoff`. Server support for `monitor` is required for that script's notification facility.

Scripts wanting to customize those offline notifications can handle the server's numeric replies using [on](../commands/on.md). Their arguments are server reply fields, not the removed hook's `$0` contract. A monitor offline report concerns watched nickname availability; it does not necessarily establish a person's reason for leaving IRC.

For quit messages you actually receive through shared channels, see [on signoff](../on/signoff.md) and [on channel_signoff](../on/channel_signoff.md). Those are separate events and do not replace server monitoring of nicknames outside your channels.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records removal of the previously sunset notify signon and signoff hooks.
- [source/hook.c](../../source/hook.c): the named-hook table omits `notify_signoff`; `find_hook` rejects unknown event names.
- [include/hook.h](../../include/hook.h): current event identifiers contain no notify-signoff hook.
- [script/notify](../../script/notify): defines the monitor wrapper and directly formats numeric `731` without emitting the former event.
- [script/notify.jm](../../script/notify.jm): retains references to the removed event.
