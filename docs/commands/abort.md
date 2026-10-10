# abort command

## Synopsis

```text
/abort
```

## Description

This command calls the C library's `abort(3)` function, abnormally terminating the client. You should not expect it to return. It is intended for debugging purposes, such as obtaining a core dump for investigation.

A core dump is not guaranteed: whether one is written, and where it is stored, depends on the operating system, resource limits, and crash-reporting configuration.

There are no options or required arguments. The built-in handler ignores any supplied text and calls `abort()` unconditionally, without confirmation or a diagnostic of its own. It is available in every build and does not require a server connection.

The command does not call EPIC's normal exit routine, so it does not perform that routine's exit hooks, graceful server shutdown, or terminal cleanup. Connections end as a consequence of process termination; the way an IRC server reports the disconnection depends on the server. Use [`quit`](quit.md) for a normal exit.

## Example

To deliberately terminate a client being used for debugging:

```text
/abort
```

Do not expect any commands following it in a script to run.

## History

The `abort` command first appeared in “plus-9” (post-ircII, pre-EPIC).

## Source

See [source/commands.c](../../source/commands.c), where `irc_command` registers `abort` and `abortcmd()` calls `abort()` directly. For comparison, `e_quit()` calls `irc_exit()` in [source/irc.c](../../source/irc.c), which implements normal exit hooks and cleanup.
