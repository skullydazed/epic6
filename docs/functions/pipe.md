# pipe function

## Synopsis

```text
$pipe(<program-and-arguments>)
```

Load its script implementation first:

```irc
/load pipe
```

## Description

`pipe()` runs a local external command through [`exec`](../commands/exec.md), waits for the process, and returns captured standard output. It is defined by the [`pipe` script](../scripts/pipe.md), not registered as a C built-in function. The original reference to the `pipe(8)` script meant the EPIC script collection, not the operating system's pipe system call or a system manual command.

For each invocation, the alias increments `pipe.intval`, creates a process name such as `pipe1`, installs a temporary EXEC hook for that name, and starts `exec -name <name> <arguments>`. It waits using [`wait %process`](../commands/wait.md), removes the capture hook, and returns the collected text.

## Return value and limits

The result contains standard-output lines appended by `push`. Normal line terminators are removed by the exec reader, and captured lines are joined as words rather than preserved as a multiline byte stream. Empty lines are omitted; quoted-word settings can add quoting or escaping around appended text. Encoding conversion and exec output limits also apply.

Standard error follows separate EXEC_ERRORS handling and is not included. The wrapper does not return or check the process exit status, so empty successful output and some execution failures are not distinguished by a dedicated success value.

The call waits synchronously, while EPIC continues servicing events through its nested event loop. A command that never finishes can leave the call waiting indefinitely; the script supplies no timeout or capture-size limit of its own. Other callbacks can run during the wait, and its `bless`-based result collection is subject to the documented [locked-scope rules](../commands/bless.md).

Arguments retain normal exec option processing and normally run through the configured shell and shell flags. Shell syntax and the execution environment therefore affect the result. Options that change the generated process name or install a separate stdout callback can interfere with collection. The temporary hook replaces any existing hook with the same serial-zero pattern without saving it for restoration.

## Example

```irc
/load pipe
/eval echo Result: $pipe(echo hello)
```

On a system whose configured shell provides `echo`, this captures `hello` from a local command. The explicit `eval` requests script expansion at the interactive prompt.

## History

The retained script is marked as hop's 1997 work. The original DokuWiki revision identifier was `pipe_function.txt,v 1.2 2006/08/19 03:58:07 sthalik Exp`.

## Source references

- [script/pipe](../../script/pipe): process naming, stdout hook, wait, cleanup, and return value.
- [source/functions.c](../../source/functions.c): no C `PIPE` registration; `function_push()` controls joining.
- [source/exec.c](../../source/exec.c): stdout/stderr separation, line handling, shell execution, and output limits.
- [source/commands.c](../../source/commands.c) and [source/alias.c](../../source/alias.c): process waiting and locked-scope access.
