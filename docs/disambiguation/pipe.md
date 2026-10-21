# Pipe

For `$pipe(command and arguments)`, see the [pipe script](../scripts/pipe.md). Load it with:

```text
/load pipe
```

The function is an alias supplied by that script, not a registered C built-in. The “pipe function” and “pipe script” therefore refer to the same supplied interface: it runs an [exec](../commands/exec.md) command, collects stdout lines through a temporary hook, waits for the managed process, and returns the collected text.

Its result is a word-list-style accumulation, not byte-for-byte output: line boundaries and empty lines are not preserved, stderr is separate, and the function does not return an exit-status success flag. Command interpretation follows exec options and shell settings. See the script page for waiting, quoting, and capture limitations.

For direct input/output/error stream handles instead, see the distinct [exec function](../functions/exec.md). Those pipes are blocking and do not use the script's temporary exec hook. Both interfaces run local programs rather than requesting data from an IRC server.

## Source references

- `script/pipe`: function alias, temporary stdout hook, process wait, and return value.
- `source/functions.c`: built-in registry and separate `exec()` function.
- `source/exec.c`: managed subprocesses and stdout delivery.
