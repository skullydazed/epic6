# on mode

## Synopsis

```text
/on [<noise>]mode "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC receives an IRC `mode` message reporting channel or user mode changes. This includes changes to your user modes and observed channel changes. Sending a [mode](../commands/mode.md) request does not invoke the hook before the server reports a change. Mode-query numeric replies use their own numeric handling rather than this event.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the sender, target, mode string, and arguments together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender issuing or reporting the change, normally a nickname or server name. |
| `$1` | Affected channel or nickname. |
| `$2` | Mode-change string, including signs and possibly multiple mode letters. |
| `$3-` | Mode arguments, if present. |

Mode meanings and argument requirements depend on the server. The handler joins trailing parameters before invoking the hook; it does not emit a separate `mode` event for each letter. A missing target is malformed; absent or empty change text is silently ignored.

## Default behavior and timing

If the hook permits default handling, EPIC displays `Mode change "changes" on channel target by sender` for a channel, or `Mode change "changes" for user target by sender` otherwise, with its usual message prefix. Here `changes` includes mode arguments. A suppressing hook, such as a serial-zero `^mode` hook, prevents the line.

The channel or user-mode cache is updated after this hook returns, even if display was suppressed. Queries during the hook can therefore still see the earlier mode state. Suppression does not reject or reverse the server's change. Channel output uses the channel's context; user-mode output has no channel target. Both use the `mode` message level.

When [set mode_stripper](../set/mode_stripper.md) is on, EPIC first generates [on mode_stripped](mode_stripped.md) events for individual changes. The setting is off by default. Those events do not replace this combined event: their return values are ignored, and `mode` still runs afterward. Use them when per-letter handling is needed, with the server's mode classification determining how arguments are assigned.

The hook does not itself send an IRC request. Any script that responds with another mode command sends a new request subject to server permissions and policy.

## Example

Distinguish your user-mode changes from channel-mode changes:

```text
on ^mode "*" (sender, target, changes) {
    if (target == servernick()) {
        xecho -b User mode change for $target: $changes;
    } else {
        xecho -b Mode change for $target by $sender: $changes;
    };
};
```

The final named argument receives the mode string and its arguments together.

## Source references

- [source/parse.c](../../source/parse.c): `p_mode` assembles parameters, optionally invokes `strip_modes`, invokes the combined hook, displays output, and updates caches afterward.
- [source/names.c](../../source/names.c): `update_channel_mode` and `decifer_mode` update channel mode state using server-specific classifications.
- [source/server.c](../../source/server.c): `update_server_umode` updates recorded user modes.
- [include/config.h](../../include/config.h): defines `mode_stripper` off by default.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
