# on operwall

## Synopsis

```text
/on [<noise>]operwall "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for an incoming IRC `wallops` message whose text begins exactly `OPERWALL - `. This convention is used for operator broadcasts on some servers, historically EFNet servers. The event depends on the server delivering that form of message; EPIC does not classify an arbitrary operator notice as an operwall.

This is a special branch of wallops processing, not a dedicated incoming `operwall` command handler. Use standard [on](../commands/on.md) matching and noise controls. The pattern matches sender and stripped message body together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Message sender, normally an operator nickname or server name. |
| `$1-` | Message text after the exact `OPERWALL - ` prefix. |

The prefix comparison is case-sensitive. The prefix itself is omitted from this event's arguments, but retained for later wallops handling. A missing message parameter is treated as malformed and does not generate the event.

## Default behavior and suppression

There is no direct built-in display for `operwall`. If it permits default handling, EPIC continues to [on wallops](wallops.md), passing the original message with its operwall prefix. If that event also permits default handling, ordinary wallops formatting displays the message.

A suppressing hook, such as a serial-zero `^operwall` hook, prevents the later wallops event and its built-in line. Merely registering a nonsuppressing handler does not stop that chain, so both events can run for the same message.

This event runs at the `operwall` output level without a channel target. If processing continues, the wallops stage switches to the `wallop` level. The hook does not send an IRC response or alter operator privileges. Receipt and broadcast scope depend on server policy and your server-side subscriptions.

## Example

Replace the ordinary wallops display for recognized operwalls:

```text
on ^operwall "*" (sender, message) {
    echo Operator broadcast from $sender: $message;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_wallops` recognizes the exact prefix, invokes `operwall` with the stripped body, and conditionally continues to wallops handling; the command table dispatches `wallops` to this handler.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
- [source/levels.c](../../source/levels.c): registers the separate operwall and wallop output levels.
