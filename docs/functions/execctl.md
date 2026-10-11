# $execctl()

## Synopsis

`$execctl(<operation> [<arguments> ...])`

## Technical

Inspect and control processes managed by the [/exec command](../commands/exec.md), or create a process description before launching it. These processes are separate from the three stream handles returned by [$exec()](exec.md).

Operation names are case-insensitive and accept prefixes. Ambiguous prefixes select the first matching operation: for example, `ref` selects `refnum`, not `refnums`. Field names are case-insensitive but must be complete. Operations other than `refnum` take a plain integer process reference, without `%`.

## Options

| Operation | Effect and return value |
| --- | --- |
| `refnum <description>` | Resolve an exact `%number` or `%logical-name`, case-insensitively. Returns its integer reference, or `0` if not found. |
| `refnums` | List existing numeric process descriptions such as `%1 %2`, including unlaunched descriptions. |
| `new <commands>` | Store the remaining command text without launching it; return a new positive process reference. |
| `launch <refnum>` | Start an unlaunched description. Returns empty, including on success; calling it on an already launched process does nothing. |
| `closein <refnum>` | Close the pipe feeding the child's standard input. Returns empty. |
| `closeout <refnum>` | Close both output pipes and mark output ignored. Returns empty; this does not terminate the child. |
| `signal <refnum> <signal>` | Accept a numeric signal or a signal name. Returns `1` when a supported signal is passed to the signaling helper, or `0` for an invalid signal. This does not establish that the operating system delivered it. |
| `get <refnum> <field>` | Return a property's value. |
| `set <refnum> <field> <value>` | Change a writable property, normally returning `1` on acceptance or `0` on rejection. |

Signaling a description that has not launched marks it exited and disowned instead of sending an operating system signal. For a launched child, signaling targets both its process group and its process ID.

### Properties

| Field | Meaning and writable values |
| --- | --- |
| `refnum`, `refnum_desc` | Read-only numeric reference and its `%` description. |
| `logical`, `logical_desc` | Optional name and `%` name. Set `logical` to one word; names must be unique, case-insensitively, even against the process's existing name. `logical_desc` is read-only. |
| `commands` | Command text. Set to the remaining text only before launch. |
| `direct` | `0` uses the configured shell when available; `1` runs the command directly. Set to `0` or `1` only before launch. Initially `0`. |
| `window_refnum` | Output window selection. Set to a positive integer; existence is not checked. Initially `0`. |
| `server_refnum` | Server context. Set to a nonnegative integer; existence is not checked. Launch overwrites it with the current server context. Initially `-1`. |
| `stdoutc`, `stderrc` | Remaining text becomes commands run for complete lines from standard output or standard error. The line, without trailing CR/LF, is their argument text. A nonempty callback replaces the corresponding normal hook and display. |
| `stdoutpc`, `stderrpc` | Stored callback text for partial lines. Current line-buffered processing does not invoke these callbacks. |
| `exitpc` | Remaining text becomes commands run at cleanup, with arguments `<logical-name-or-number> <termination-signal> <exit-code>`. A nonempty callback replaces normal `exec_exit` hook handling. |
| `lines_limit` | Set to a nonnegative received-line limit, shared by both output streams. `0` uses `shell_limit`. Reaching a positive limit closes the process's pipes and ignores further output. |
| `redirect` | Set to exactly uppercase `PRIVMSG` or `NOTICE`. Currently suppresses normal display; current output handling does not forward the lines to IRC. |
| `who` | Set to one word identifying an intended redirection target. Stored, but unused by current output handling. |
| `pid` | Read-only operating system process ID; `-1` before launch. |
| `started_at` | Read-only creation or most recent launch timestamp as `<seconds> <nanoseconds>`. |
| `p_stdin`, `p_stdout`, `p_stderr` | Read-only operating system descriptors, initially `-1` and also `-1` after closing. These are not EPIC file handles for `$read()` or `$close()`. |
| `exited`, `termsig`, `retcode` | Read-only exit state, termination signal, and exit code. Initially zero; do not interpret an initial zero exit code as successful completion. |
| `dumb` | Read-only flag indicating that output is ignored or both output pipes have closed. |
| `lines_recvd`, `lines_sent` | Read-only counters. Received lines count both streams; the current implementation never increments `lines_sent`. |
| `disowned` | Read-only internal flag, including the administrative termination of an unlaunched description. |

## Restrictions

Missing required arguments, an unknown operation or field, an invalid process reference, or an unsupported setter returns empty. `waitcmds` is not an implemented field. The process reference disappears when cleanup removes its description.

`launch` does not wait for completion or return its internal startup result. Pipe or fork failures print a diagnostic. Use [/wait](../commands/wait.md) or an exit callback for asynchronous completion. Callbacks run in the process's output/server context with their own local scope; they are command bodies, rather than function return expressions.

## Examples

Inspect existing processes without launching anything:

```irc
fe ($execctl(refnums)) description {
    @ process = execctl(refnum $description)
    echo $description: $execctl(get $process commands)
}
```

To configure an unlaunched description, store the result of `new`, then set properties before requesting launch:

```irc
@ process = execctl(new echo hello)
@ execctl(set $process direct 1)
@ execctl(launch $process)
```

## Source

Registration and wrapper: [source/functions.c](../../source/functions.c). Operation dispatch, process creation, launching, callbacks, and cleanup: [source/exec.c](../../source/exec.c).
