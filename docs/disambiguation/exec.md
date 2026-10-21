# Exec

Do you mean:

- The [exec command](../commands/exec.md), `/exec [options] command`, which manages asynchronous subprocesses and integrates their output with the client's event loop?
- The [exec function](../functions/exec.md), `$exec(program [arguments...])`, which directly launches a program and returns three EPIC file handles for its input, output, and error streams? These pipes are blocking and are separate from command-managed process references.

Related interfaces are [execctl](../functions/execctl.md), for command-managed process descriptions, and [on exec](../on/exec.md), for stdout lines from those processes. Standard error and termination use [exec_errors](../on/exec_errors.md) and [exec_exit](../on/exec_exit.md).

The command normally uses the configured shell unless direct execution is selected. The function does not implicitly use a shell. Both run local programs; output reaches IRC only when explicitly directed there. Consult the selected interface for quoting, callbacks, results, and cleanup.

## Source references

- `source/exec.c`: asynchronous command-managed processes and output hooks.
- `source/functions.c`: `exec()` stream-handle interface and `execctl()` registration.
- `source/files.c`: external-program stream setup and file-handle operations.
