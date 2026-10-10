# exec

Run an external program as an asynchronous subprocess of EPIC, read its output, and optionally send it input.

## Synopsis

```text
/exec
/exec [options] command [arguments...]
/exec [options] (command [arguments...]) [input text]
/exec [options] %process [input text]
```

With no arguments, `exec` lists the registered processes, including their EPIC reference numbers, optional logical names, operating-system PIDs, and commands. `%process` is an EPIC reference such as `%1` or a logical name such as `%job`; it is not an operating-system PID. Names are case-insensitive.

## Description

A new command runs in the background with separate pipes for standard input, standard output, and standard error. EPIC continues accepting commands while it runs. By default, `/set shell` selects the shell and `/set shell_flags` supplies its option argument; the defaults are `/bin/sh` and `-c`. Shell syntax such as pipelines and filename expansion is therefore available. EPIC's usual script expansion happens before the command reaches the shell.

With `-direct`, or when `shell` is unset, EPIC executes the program directly using the executable search path. Double quotes group arguments in this mode; shell expansion, pipelines, and shell built-ins are unavailable. The child inherits the environment with `TERM` set to `tty`. Its input and output are pipes, not a terminal, so full-screen terminal programs are unsuitable.

Options precede the command or process reference and are applied in order. Option arguments such as names and targets are single words; callback bodies require braces. Prefer full option names: abbreviations use the first matching option, and unknown options are silently discarded. Parentheses delimit a command separately from trailing input text; they do not create a shell subshell by themselves.

An existing process reference changes that process instead of starting another. A process created with `-nolaunch` can be launched later with `/exec %name`. Changing launch options on a process that has already started does not restart it.

## Options

| Option | Effect |
| --- | --- |
| `-name name` | Give the process a logical name, or rename it. Names must be unique among registered processes. |
| `-direct` | Execute the program without a shell when it is launched. |
| `-nolaunch` | Register or configure the process without launching it in this invocation. |
| `-window` | Set the output window using the process's current window context. |
| `-wintarget window` | Set the output window to a window name or reference accepted by EPIC's window lookup. |
| `-line {commands}` | Run the block for each complete stdout line, with the line as `$*`, replacing the normal stdout hook and display. |
| `-error {commands}` | Run the block for each complete stderr line, with the line as `$*`, replacing the normal stderr hook and display. |
| `-linepart {commands}` | Store a partial-stdout callback. The current line-buffered reader does not invoke it. |
| `-errorpart {commands}` | Store a partial-stderr callback. The current line-buffered reader does not invoke it. |
| `-end {commands}` | Append a termination callback, like a process wait callback. Multiple blocks accumulate. |
| `-exit {commands}` | Set the termination callback that replaces `exec_exit` and the default termination notification. A later block replaces the earlier one. |
| `-in` | Send the trailing input text, followed by a newline, to an already launched, named process. Input is echoed locally subject to `send_exec`. |
| `-closein` | Close the child's input pipe, delivering end-of-file on its standard input. |
| `-closeout` | Stop reading both stdout and stderr. |
| `-close` | Close all three pipes. This does not itself send a termination signal. |
| `-limit number` | Limit the total stdout and stderr lines read. A positive value closes the pipes once the limit is reached; zero uses `shell_limit`, and a negative value disables the limit for this process. |
| `-signalname` or `-number` | Send a platform-supported signal to the process and its process group, for example `-term` or `-9`. The separate form `-signal term` is not supported. |
| `-out` | Store a `privmsg` destination from the current channel or query and suppress default local output. See the redirection limitation below. |
| `-msg target` | Store a `privmsg` destination and suppress default local output. See below. |
| `-notice target` | Store a `notice` destination and suppress default local output. See below. |

**Redirection limitation:** The current C output reader does not send lines to the destinations stored by `-out`, `-msg`, or `-notice`. These options suppress default local display, but do not themselves produce IRC messages. Use a `-line` or `-error` callback with an explicit [msg](msg.md) or [notice](notice.md) command when forwarding is wanted. Setting a window does not clear this stored redirection state.

`-in` uses the process's logical name internally, so an unnamed process cannot receive input through this option even when selected by number. Give it a name first. Options execute before a new process starts, so `-in` cannot supply initial input in the same invocation that launches the program.

## Output and termination

Without a callback, complete stdout lines go through the `exec` hook and stderr lines through `exec_errors`. Hook arguments are the logical name or EPIC reference number, followed by the line. Trailing CR and LF characters are removed. Output uses the process's saved server context and selected window, or ordinary window routing if none was selected. Process-specific [encoding](encoding.md) rules apply to input and output.

The default `shell_limit` is zero, meaning no output limit. A per-process limit of zero follows that global setting. Reaching a positive limit closes the pipes; it does not send a signal or guarantee that the program exits.

EPIC keeps a terminated process registered until its output pipes have also finished, so buffered output can be consumed. Termination callbacks receive `$*` containing three words: the logical name or EPIC reference number without `%`, the terminating signal number, and the exit status. Accumulated `-end` callbacks run first, in order; then `-exit` runs, or otherwise the `exec_exit` hook runs. If that hook permits it, `notify_on_termination` controls the usual termination message and defaults to on.

## Restrictions

This built-in is always registered, but execution requires the platform's Unix process facilities: pipes, `fork`, executable launching, child reaping, and signals. Shell and program availability depend on the host. Signal names and numbers also depend on the host; requesting a signal does not make termination synchronous.

Invalid process references, duplicate names, nonexistent windows, missing required target or callback arguments, unavailable executables, and pipe or fork failures can produce diagnostics. Diagnostics for an option do not necessarily cancel the rest of the command. Closing pipes can make a program finish or fail, but a program may continue running afterward. Use signal options on already launched processes; combine `-nolaunch` with a signal option if you intend to discard an unlaunched registration without starting it.

## Examples

```text
/exec echo hello
/exec -direct /bin/echo "hello world"
/exec -name job -line {echo stdout: $*} -error {echo stderr: $*} echo hello
/exec -name reader -nolaunch cat
/exec %reader
/exec -in %reader hello
/exec -closein %reader
/exec -name sleeper sleep 60
/exec -term %sleeper
```

The `reader` example starts a program that copies input to output, sends it one line, and closes its input so it can finish. The executable names in these examples must be available on your host.

## Related commands and source

See [wait](wait.md), [on](on.md), [query](query.md), and [window](window.md).

The registration is in [source/commands.c](../../source/commands.c); parsing, subprocess management, callbacks, and output handling are in [source/exec.c](../../source/exec.c). Settings are registered in [source/vars.c](../../source/vars.c), with defaults in [include/config.h](../../include/config.h). Allocation initialization and direct-command argument parsing are in [source/ircaux.c](../../source/ircaux.c).
