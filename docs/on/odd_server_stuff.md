# on odd_server_stuff

## Synopsis

```text
/on [<noise>]odd_server_stuff "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC sends an unrecognized, unsupported, or malformed server message to its odd-message handler. It is useful for handling server extensions without a dedicated incoming handler, or customizing diagnostics for unexpected messages.

This is not an interception point for arbitrary raw server output. Recognized messages normally use their own handlers and events. For every incoming line before normal parsing, use [on raw_irc](raw_irc.md); suppressing that earlier event can prevent this one from running too.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches sender, command, and remaining argument text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender prefix name, or `*` if absent or empty. |
| `$1` | Command token, or `*` if absent or empty. |
| `$2-` | Remaining arguments joined into text, possibly empty. |

The sender can be a server or nickname; it is not necessarily your connected server's name. These are parsed message components, not an intact wire line. Prefix userhost information, IRC tags, and the original trailing-parameter colon are not included in these three fields as raw framing. A handler that detects a malformed message can already have joined or otherwise processed arguments before calling this event.

## When it runs

The common dispatcher invokes this event for commands with no active incoming handler, as well as certain malformed command or sender structures. Individual command and numeric handlers also invoke it when required parameters are missing or a reply violates a check. The exact fields therefore depend on where parsing stopped.

A server extension being reported here does not necessarily mean the server is broken; it can mean the client has no dedicated implementation for that command. Conversely, not every bad or unusual message is guaranteed to use this event, since individual handlers can silently ignore some cases.

## Default behavior

If the hook permits default handling, EPIC displays `Odd server stuff: "command arguments" (sender)` with its usual message prefix. A suppressing hook, such as a serial-zero `^odd_server_stuff` hook, prevents that diagnostic and can supply replacement behavior.

Suppression does not resume the rejected built-in parser, implement the server extension automatically, or undo state changes made before the event. The hook does not itself send an IRC request.

## Example

Replace odd-message diagnostics while retaining the parsed command and sender:

```text
on ^odd_server_stuff "*" (sender, command, arguments) {
    echo Unhandled $command from $sender: $arguments;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `rfc1459_odd` assembles arguments, substitutes missing tokens, invokes the hook, and prints default output; `parse_server` dispatches unsupported and malformed messages and invokes `raw_irc` first.
- [source/numbers.c](../../source/numbers.c): numeric validation paths call `rfc1459_odd` for malformed replies.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
