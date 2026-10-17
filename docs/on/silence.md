# on silence

## Synopsis

```text
/on [<noise>]silence "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when the client receives a server `silence` message acknowledging a change to a silence pattern. Support for the command, accepted patterns, and which replies are sent depend on the IRC server.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the sign and silence pattern together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | `+` if a pattern was added, or `-` if one was removed. |
| `$1` | Pattern silenced or unsilenced. |

EPIC takes the first character of the first incoming parameter as the sign and the remainder as the pattern. It does not validate the sign or include the sender or any later parameters in this hook. An unexpected first character therefore also reaches the hook. A message with no parameter is rejected before the hook runs.

## Default behavior

If the hook permits default handling, EPIC displays a line saying that you will no longer receive messages from the pattern for `+`, or will now receive them for `-`. An unexpected sign produces `Unrecognized silence argument: pattern`. A suppressing hook, such as a serial-zero `^silence` hook, prevents this built-in line.

Suppression affects local output only. It does not reverse the server's silence change. The hook itself sends no request and maintains no local silence list.

The [silence](../commands/silence.md) command forwards its arguments to the server; issuing it does not itself trigger this hook. Numeric silence-list replies such as `271` use numbered hooks and [on numeric](numeric.md), not `on silence`.

## Example

Replace the acknowledgement display:

```text
on ^silence "*" (sign, pattern) {
    echo Silence change: $sign $pattern;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_silence` extracts the sign and pattern, invokes the hook, and displays the acknowledgement.
- [source/commands.c](../../source/commands.c): `silence` uses `send_comm` to forward the command to the server.
- [source/numbers.c](../../source/numbers.c): numeric `271` follows numeric handling and has its own default display.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching and suppression.
