# on mode_stripped

## Synopsis

```text
/on [<noise>]mode_stripped "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Similar to [on mode](mode.md), but reports one signed mode letter at a time from an incoming IRC mode-change message. It is useful for scripts evaluating individual changes within a combined mode string.

This event requires [set mode_stripper](../set/mode_stripper.md) to be on; it is off by default. It supplements the combined `mode` event rather than replacing it. Mode-query numeric replies do not use this path.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the sender, target, signed letter, and optional argument together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender issuing or reporting the change, normally a nickname or server name. |
| `$1` | Affected channel or nickname. |
| `$2` | Sign and one mode letter, such as `+o` or `-v`. |
| `$3` | Argument assigned to that channel-mode change, if any. |

Signs in the combined mode string update the sign for following letters and do not generate their own events. If no sign precedes a letter, this parser initially assumes `+`. Events follow the order of letters in the mode string.

## Argument assignment and limitations

For channel modes, EPIC uses the server's `prefix` and `chanmodes` information to determine which letters consume arguments. Prefix membership modes and the first two channel-mode groups consume an argument on both addition and removal. The third group consumes an argument only when adding; the fourth consumes none. Without server information, EPIC falls back to prefix modes `ohv` and channel groups `b,k,l,imnpst`.

An unknown mode still produces an event but consumes no argument here, so incomplete or incorrect server classifications can misalign subsequent arguments. A missing required argument becomes empty text. For user modes, every letter produces an event without an argument; trailing user-mode arguments are not assigned to individual events.

This is parsing of the received message, not confirmation that every individual change is meaningful or that the local cache fully supports it. Mode meanings remain server-dependent.

## Default behavior and ordering

There is no built-in output for `mode_stripped`, and the caller ignores its default-handling return value. A suppressing hook does not prevent later per-letter events, the combined `mode` event, its display, or the mode-cache update. Suppress the combined `mode` event separately when replacing that display.

All stripped events run before `mode`, and the cache is updated afterward. Mode queries during these handlers can still return the old state. Channel events use the channel's output context; user-mode events have no channel target. Both use the `mode` message level. The hook does not send an IRC request.

## Example

```text
set mode_stripper on;
on ^mode_stripped "% % +o *" (sender, channel, change, nick) {
    echo $sender granted operator status to $nick on $channel;
};
```

For a server that classifies `o` as a prefix membership mode, an incoming `+oo alice bob` produces two events. The normal combined mode display remains unless separately suppressed.

## Source references

- [source/parse.c](../../source/parse.c): `p_mode` calls `strip_modes` before the combined event; `strip_modes` separates signs, letters, and arguments while ignoring hook return values.
- [source/names.c](../../source/names.c): `chanmodetype` uses server mode classifications and fallback defaults.
- [include/config.h](../../include/config.h): defines `mode_stripper` off by default.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching.
