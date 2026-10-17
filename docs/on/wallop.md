# on wallop

## Synopsis

```text
/on [<noise>]wallop "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when the client receives an IRC `wallops` message, unless an earlier [on operwall](operwall.md) hook suppresses its processing. Wallops are broadcasts commonly associated with IRC operators; who can send or receive them and their broadcast scope depend on the server's policy and user modes.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the sender, sender marker, and message together. The hook name is singular `wallop`, although the incoming protocol command is `wallops`. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Message sender, normally a nickname or server name. |
| `$1` | `S` if the sender name contains a dot, otherwise `*`. |
| `$2-` | Message text. |

The marker is a naming heuristic, not an independent check of operator privileges. A missing message parameter is rejected before the hook runs; an explicitly empty message is accepted.

## Default behavior

If the hook permits default handling, EPIC prints `!sender*! message` for the `*` marker or `!server! message` for the `S` marker. A suppressing hook, such as a serial-zero `^wallop` hook, prevents this built-in line.

A message beginning exactly `OPERWALL - ` first invokes `operwall`, with that prefix removed from its arguments. If that event permits continued handling, `wallop` receives the original message including the prefix. Suppression by `operwall` prevents `wallop` and its default output altogether.

This event runs in the receiving server's context at the `wallop` output level without a channel target. The hook does not send a response, change user modes, or grant operator privileges. Issuing [wallops](../commands/wallops.md) sends a request to the server and does not itself invoke this hook; any received broadcast depends on the server.

## Example

Replace the built-in wallops display:

```text
on ^wallop "*" (sender, kind, text) {
    echo Broadcast from $sender ($kind): $text;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_wallops` classifies the sender, conditionally invokes `operwall`, invokes `wallop`, and displays the default line.
- [source/commands.c](../../source/commands.c): `e_wallop` sends the `wallops` request.
- [source/hook.c](../../source/hook.c): singular event registration and standard hook matching and suppression.
- [source/levels.c](../../source/levels.c): separate `wallop` and `operwall` output levels.
