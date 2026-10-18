# on yell

## Synopsis

```text
/on [<noise>]yell "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for client diagnostic messages offered through EPIC's `yell` output path, including selected debugging messages and system-error diagnostics. Scripts can use it to observe or suppress unwanted diagnostic chatter. It is not a hook for every error or every line printed by the client.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the complete diagnostic text. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$*` | Formatted diagnostic text. |

There is no separate severity, server refnum, or error-code field. System-error diagnostics include the configured banner and `INFO --` prefix in this text; ordinary `yell` messages do not automatically add them.

## Suppression and limitations

For ordinary `yell` output, a suppressing hook, such as a serial-zero `^yell` hook, prevents the diagnostic from being passed to the normal logging and window-output path. It does not undo the condition or operation that produced it. If permitted, the message is passed onward and can still encounter [on window](window.md).

The same hook is used by debug output when its relevant debug flag is enabled, and by system-error output. Ordinary `yell` and enabled debug output do not honor ordinary display suppression; system-error output checks display permission first and uses the `syserr` output level. Ordinary `yell` inherits its caller's output context, so not every event has that level.

Load-error reporting through `my_error` also invokes this hook, but ignores its suppression result and prints the diagnostic afterward. Any load-stack output produced before that event is separate. Privileged internal diagnostics bypass `on yell` entirely. Consequently, `on ^yell` does not suppress every diagnostic.

The event is not marked nonrecursive. A handler that causes another diagnostic can invoke it again; keep handlers simple and avoid commands that produce further errors.

## Example

Suppress one known diagnostic pattern while leaving other messages alone:

```text
on ^yell "A diagnostic prefix you recognize*" {
};
```

Choose a pattern from the actual diagnostic text you want to filter. This only vetoes output from callers that honor the hook result, as described above.

## Source references

- [source/output.c](../../source/output.c): `yell`, `debug`, and `vsyserr` honor suppression; `my_error` invokes the event but ignores its result; `privileged_yell` bypasses it.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching and suppression.
- [source/screen.c](../../source/screen.c): later window-output handling.
