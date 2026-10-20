# less-pipe

## Synopsis

```text
/load less-pipe
/less_pipe <process-name> <shell-command>
```

## Description

This script defines `less_pipe`, with an underscore, to run a named [exec](../commands/exec.md) process and page its captured standard output. It does not define an alias named `less-pipe`. The first argument is the process's logical name; the remaining arguments are passed to `exec -name` as the command to run.

At least a process name and one command word are required. Missing arguments produce no usage message. The alias resets its global line counter even when it does not launch anything. It uses `winsize` from [builtins](builtins.md), normally supplied by default startup.

The process runs asynchronously using the ordinary exec shell and shell-flags configuration. Shell syntax and pipelines follow that configuration, and EPIC's usual command expansion happens before the shell receives the text. This is for commands you intend to execute, not for displaying untrusted text as a command. An existing process with the same logical name or other exec errors can prevent launch; the script provides no recovery or cleanup for such failures.

## Capture and paging

The script installs suppressing hooks matching the process name. Each `exec` stdout event stores its text in a numbered `lessline` script variable. It does not display stdout as it arrives. When `exec_exit` runs, it removes those process-specific hooks and calls `less_array` to show the buffered output. A process which continues running can therefore accumulate output without ever starting the pager.

Standard error is handled separately and displayed immediately as `*** Error on exec: <process-and-line>`. It is not included in the paged stdout buffer. Process termination display is suppressed by this script's exit hook.

Each page displays up to the current window's display-height count of stored logical lines. Wrapped lines are not counted by their actual screen height, so a page can scroll more than one visible windowful. Displayed entries are deleted from the buffer. The prompt says `Enter q to quit, or anything else to continue`; another character shows the next page. A page that ends exactly at the budget can prompt once more even when there are no remaining entries.

Output uses ordinary `echo` rather than a saved `xecho -w` destination. The first page inherits the exec exit event's normal `other`-level context; subsequent prompt callbacks likewise use their current output context. This does not lock output to the window where the command was started, and changing windows or routing settings can affect where later output appears.

## Concurrency and cleanup limits

Despite the separate process names, this implementation is not safely re-entrant. It shares a global `line` counter, the `lessline` buffer, and the continuation state `aaa.array_name`, `aaa.next_line`, and `aaa.final_line`. Starting another invocation can reset or overwrite an active capture or pending pager. Use only one invocation at a time.

Pressing `q` stops paging but does not delete the unshown stored entries. It does not terminate the child process: paging normally begins only after that process has already finished. The helper removes displayed entries, but there is no complete buffer cleanup or automatic reset of all continuation variables after quitting.

Hooks are installed directly, without saving any existing handlers for the same patterns. Cleanup removes those patterns rather than restoring earlier definitions. Other exec callbacks or replacement hooks can also prevent capture or completion. If launch fails, the expected exit event may never occur and the newly installed hooks can remain.

The script has no size limit on its own capture buffer; ordinary [shell_limit](../set/shell_limit.md) and exec output limits still apply. File or process output without complete newline-terminated lines is subject to the current exec reader's line-buffering behavior. There is no backward paging, search facility, success return value, or script-specific setting. The script itself sends no IRC requests.

## Example

```text
/load less-pipe
/less_pipe listing ls -l
```

Start a process named `listing`, collect its stdout, and page it after process cleanup. The command is interpreted by the configured exec shell. Run another pager only after the current one has finished or been dismissed.

## History

Written for EPIC4 by hop in 1997, based on the [less](less.md) script.

## Source references

- [script/less-pipe](../../script/less-pipe): actual alias name, named-process hooks, shared buffer, and pager.
- [source/exec.c](../../source/exec.c): stdout/stderr events, output limits, process context, and exit dispatch.
- [source/commands.c](../../source/commands.c) and [source/screen.c](../../source/screen.c): exec command registration, ordinary output, and character-input callbacks.
- [script/builtins](../../script/builtins): `winsize` compatibility function.
