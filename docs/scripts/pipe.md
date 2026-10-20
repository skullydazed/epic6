# pipe script

## Synopsis

```text
/load pipe
$pipe(<exec command and arguments>)
```

## Description

This script implements the `pipe` function: it captures standard output from an [exec](../commands/exec.md) command and returns it as a string. It runs a local external process, not an IRC server request.

For each call, the script increments `pipe.intval`, creates a process name such as `pipe1`, installs a temporary [exec](../on/exec.md) hook for that name, and starts `exec -name <name> <arguments>`. It then waits for that process to be removed before deleting the hook and returning the collected text.

## Return value

Each received standard-output line is appended to the result with the script's `push` command. Line-ending carriage returns and newlines have already been removed by the exec reader. With ordinary word handling, multiple lines become a single space-joined string rather than a multiline value; empty lines are omitted. Enabling `xdebug dword` can instead quote and escape appended lines containing spaces. This is not a byte-for-byte capture interface.

Standard error is not included. It follows normal [exec_errors](../on/exec_errors.md) handling and may be displayed independently. The function does not return or check the exit status, distinguish failed execution from successful empty output, or provide a success flag. Process termination notices can also appear according to ordinary exec handling and `notify_on_termination`.

The current exec reader is line-buffered. Partial output without a newline is not delivered immediately, so do not use this function to capture interactive prompts. Normal exec output encoding conversion and limits, including `shell_limit`, apply before capture and can change or truncate the result.

## Command interpretation and requirements

Arguments are passed through to `exec`, including its option processing. By default, the command runs through the configured `shell` and `shell_flags`; shell syntax, executable availability, permissions, and environment therefore affect the result. Exec options such as `-direct` change that behavior. Options that rename the process or install a separate stdout callback can prevent the temporary hook from collecting its output; use ordinary command arguments for predictable capture.

No external helper package is loaded by this script, but a working local process-execution environment and the requested program or shell are required. The script defines no settings or timeout.

## Waiting and limitations

The caller waits synchronously, while EPIC continues running its event loop. Other input, hooks, timers, and nested script calls may execute during that wait. A process that never exits, waits for input, or leaves output descriptors open can keep the call waiting indefinitely. The function does not close the child's input, kill it after a delay, or impose a capture-size limit of its own.

The temporary suppressing hook prevents ordinary display of matching stdout lines. It is removed afterward, but any pre-existing hook with the same serial-zero pattern is not saved or restored. Process names use a global counter without checking for unrelated processes with colliding names. Avoid modifying `pipe.intval`, reusing its generated names, or changing the matching exec hook during a call. A launch failure can leave an empty result; the wrapper has no dedicated error recovery.

The internal `bless` command connects the output hook to the local result variable in the waiting call's locked stack frame. This supports collection across asynchronous exec events; it does not make the operation a background-returning function. Invoking `/pipe <command>` runs the same alias but does not display its returned string automatically; use function syntax to consume it.

## Example

On a system providing the usual `date` command:

```text
/load pipe
/echo Local date: $pipe(date +%Y-%m-%d)
```

The result is local program output, not a date obtained from an IRC server. Keep command arguments under your control because the default execution path uses a shell.

## History

The retained source is marked as 1997 work by hop.

## Source references

- [script/pipe](../../script/pipe): process naming, capture hook, waiting, and returned value.
- [source/exec.c](../../source/exec.c): process creation, shell execution, line-buffered output, output limits, callbacks, and termination handling.
- [source/commands.c](../../source/commands.c): `waitcmd` waits through event-loop processing and locks the current stack frame; `push_cmd` appends collected output.
- [source/alias.c](../../source/alias.c): `bless_local_stack` exposes the locked caller frame to the output hook.
- [source/hook.c](../../source/hook.c): temporary hook matching, replacement, suppression, and removal.
- [source/functions.c](../../source/functions.c) and [source/ircaux.c](../../source/ircaux.c): `function_push` and `malloc_strcat_word` determine joining and optional quoted-word behavior.
