# on exit

## Synopsis

```text
/on [<noise>]exit "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC begins its normal cleanup path before exiting, including [quit](../commands/quit.md). It is useful for synchronous script cleanup, such as saving script-managed state using your own persistence commands.

This hook cannot trap or cancel a quit. Its return value is ignored, and shutdown continues after the handler returns. Do not invoke `quit` again from an exit handler: recursive exit enters an emergency termination path that bypasses normal cleanup.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the complete exit text. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$*` | Explicit exit message, or the literal text `Default` when no message was supplied. |

For `/quit <message>`, the hook receives that message. For `/quit` without text, it receives `Default`; that is not necessarily the text sent to the server. Later server shutdown selects each server's configured quit message, falling back to `Leaving`, when no explicit message was provided. Server-side quit-message formatting can also make the final wire text differ from the hook argument.

Other normal exit paths can supply their own text, such as an explanation for terminal input closure. This is a client-exit event, not a notification that another user has quit IRC; see [on signoff](signoff.md) for that.

## Default behavior and ordering

There is no built-in display attached to this event. Suppressing default handling does not suppress shutdown or the later server quit messages.

The hook runs before EPIC closes its server connections, disables session logging, cleans up child processes and database handles, and destroys windows. Handlers can do immediate cleanup while those resources still exist, but work deferred to a later event-loop turn is not a reliable exit-cleanup mechanism.

The event is not guaranteed for abrupt termination, uncatchable signals, or crash paths that bypass normal cleanup. It should not be the only way important state is saved.

## Example

Display the reason immediately before shutdown:

```text
on ^exit "*" (message) {
    echo Exiting EPIC: $message;
};
```

To save script state, place the script's own synchronous persistence command in the handler before this message.

## Source references

- [source/commands.c](../../source/commands.c): `e_quit` passes explicit text or no message to `irc_exit`.
- [source/irc.c](../../source/irc.c): `irc_exit` supplies `Default`, invokes the hook, ignores its return value, and performs shutdown; its recursive and crash paths can bypass the hook.
- [source/server.c](../../source/server.c): `servers_close_all` and `server_close_internal` close connections and select or format final quit messages.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard matching.
