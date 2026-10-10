# wait command

## Synopsis

```text
/wait
/wait for commands
/wait -cmd commands
/wait %process
/wait %process -cmd commands
```

## Description

`wait` arranges for later script commands or callbacks to run after a server round trip or a subprocess has finished. Its waiting forms suspend the calling script while EPIC continues processing input, server traffic, timers, and other events through a nested event loop. They do not put the entire client to sleep like [`sleep`](sleep.md).

With no arguments, it sends a synchronization marker to the current server and waits for the corresponding reply. The marker is an intentionally unknown command; its numeric 421 reply is consumed internally. This provides a round-trip barrier for commands already sent to that server, not a guarantee that every asynchronous operation has completed.

`wait for commands` executes the remaining command text in the current scope and enclosing argument context, then performs the server wait only if that execution sent server data. `for` is recognized case-insensitively by its first three characters. It is not a special selector for a file descriptor or process; `wait for =fd` has no built-in descriptor-wait meaning.

`wait -cmd commands` queues a callback and returns immediately. The callback runs after its server marker's reply, with a fresh local scope and empty positional arguments. It does not capture the caller's local variables or argument context. The `-c` abbreviation is accepted. This mode requires a usable server connection to receive the marker reply; it is not an end-of-alias callback independent of IRC.

## Subprocess waits

`wait %process` waits while the specified [`exec`](exec.md) process remains registered with EPIC, continuing to process events. The process selector can be a supported numeric or named process reference. If it is already invalid, this form silently does nothing.

`wait %process -cmd commands` registers a callback without waiting. The literal `-cmd` is optional when remaining command text is supplied. Callbacks are queued in registration order and run during process cleanup, before the process's own exit callback or normal exit hook. They use the process's stored server/output context and their own local scope. `$0` is its logical name, or numeric reference if unnamed; `$1` is its termination signal; `$2` is its exit code.

## Restrictions

A server wait requires a registered current server; otherwise the synchronous server form returns immediately. Closing the server resets its outstanding synchronous wait counters, which can end a wait without the requested data arriving. There is no timeout option. A server that does not send the expected marker reply can leave a wait pending indefinitely. Asynchronous callbacks are not promised to execute after disconnection.

Nested synchronous waits on the same server do not return until all its outstanding synchronous markers have been answered. Events processed while waiting can run other scripts and change state, so reentrancy matters. Prefer a reply hook or command-specific callback when you need a particular result.

In particular, a server round trip does not drain EPIC's entire [`userhost`](userhost.md), [`userip`](userip.md), or [`ison`](ison.md) request queues. Later batches may be sent only after an earlier reply, after the wait marker was already sent. Remote-server and delayed replies can also outlast the barrier. Do not equate `wait` with completion of every nickname callback.

An unrecognized first argument reports `Unknown argument to /WAIT`. The command has no normal completion output or function return value and is always registered. Process waits do not themselves require an IRC connection.

## Examples

A traditional header/footer arrangement for a local channel ban-list query:

```text
alias banlist {
    echo *** Begin ban list for #blah
    mode #blah +b
    wait
    echo *** End ban list for #blah
}
```

For precise completion handling, hook the server's end-of-ban-list numeric instead of relying on the round-trip barrier.

Register a deferred server callback in an alias:

```text
alias backwards {
    wait -cmd echo this appears later
    echo this appears first
}
```

Wait for an already running named subprocess in a script:

```text
wait %worker
echo process no longer registered
```

## Source

[source/commands.c](../../source/commands.c) implements `waitcmd()` and mode parsing. [source/server.c](../../source/server.c) implements server markers, callbacks, counters, and close handling. [source/numbers.c](../../source/numbers.c) intercepts marker replies. [source/exec.c](../../source/exec.c) manages process waits and cleanup callbacks; [source/alias.c](../../source/alias.c) manages callback scopes and stack locking.
