# exit

Exit EPIC. This built-in is an alternative name for [quit](quit.md), with identical behavior.

## Synopsis

```text
/exit [reason]
```

## Description

`exit` ends the entire client session, including all server connections and windows. The optional reason is the remaining command text, not just its first word. When supplied, it becomes the quit message for every server connection. With no reason, each server uses its configured quit message, falling back to `/set quit_message`; if neither supplies one, it uses `Leaving`.

Before disconnecting, EPIC runs the `exit` hook with the supplied reason, or the word `Default` if no reason was given. The hook's return value does not cancel shutdown. For registered server connections, EPIC attempts to send an IRC `quit` message before closing the connection. Quit messages support `%s` substitution with the EPIC version. An empty resulting message suppresses the outgoing `quit` request, although the connection still closes. Delivery and the message shown to other users depend on the server.

Shutdown closes logging and database handles, restores the terminal, and removes windows and other client state. Remaining [exec](exec.md) subprocesses have their pipes closed and receive `SIGTERM`; EPIC waits two seconds, reaps exited children, then sends `SIGKILL` to those still tracked. Consequently, exiting with subprocesses can take a short time. Registered internal shutdown callbacks also run before the client exits normally with operating-system status zero.

Use [disconnect](disconnect.md) when you want to close a server connection while keeping EPIC running.

## Examples

```text
/exit
/exit Going home for the night
```

## Source

Both command names are registered to `e_quit` in [source/commands.c](../../source/commands.c). Client shutdown and its hook are implemented in [source/irc.c](../../source/irc.c); connection closing and quit-message formatting are in [source/server.c](../../source/server.c), and subprocess cleanup is in [source/exec.c](../../source/exec.c). The command is always registered.
