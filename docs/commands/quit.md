# quit

Exit IRC and terminate the entire EPIC client session.

## Synopsis

```text
/quit [quit-message]
```

## Description

When you are finished talking, `quit` disconnects from every server, closes the client, and returns control to the shell. It has the same built-in behavior as [exit](exit.md). Use [disconnect](disconnect.md) when you want to close a server connection while keeping EPIC running.

The optional quit message is the entire remaining command text. An explicit message becomes the quit message for every server. Without one, each server uses its configured message, falling back to `/set quit_message`, then `Leaving` if no message is available. During connection closing, `%s` in the message is replaced with the EPIC version. An empty resulting message omits the outgoing IRC quit request, though the connection still closes.

Shutdown performs these actions:

- Run the `exit` [on](on.md) hook with the explicit message, or `Default` when none was supplied. Its return value cannot cancel shutdown.
- Attempt to send an IRC `QUIT` for registered open server connections, then close connections. Server policy and successful delivery determine what other users see.
- Close logging and database handles and clean up [exec](exec.md) subprocesses. Their pipes are closed and they receive `SIGTERM`; EPIC waits two seconds, reaps exited children, then sends `SIGKILL` to remaining tracked processes.
- Restore the fullscreen terminal, remove windows and other client state, and run registered internal shutdown callbacks.
- Terminate normally with operating-system exit status zero.

This list describes shutdown effects rather than a contractual ordering for scripts. Exiting with tracked subprocesses can take a short time. The command does not wait for server acknowledgement and does not return to its calling script. Hooks can perform actions during shutdown; calling `quit` recursively from them forces immediate termination instead of repeating normal cleanup.

The command is always registered, has no options, and works without a live IRC connection. It produces no separate success result.

## Examples

Quit with a message:

```text
/quit I'll be back soon!
```

Quit using configured messages:

```text
/quit
```

## Source

Registration and `e_quit` are in [source/commands.c](../../source/commands.c). `irc_exit`, its hook, terminal cleanup, and shutdown callbacks are in [source/irc.c](../../source/irc.c). Per-server messages, formatting, and connection closing are in [source/server.c](../../source/server.c), with subprocess cleanup in [source/exec.c](../../source/exec.c).
