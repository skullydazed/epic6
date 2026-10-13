# pid function

## Synopsis

`$pid()`

## Technical

Returns the operating-system process ID of the running EPIC client using `getpid()`. All arguments are ignored; passing an argument does not select a subprocess or request the parent process ID. Use [$ppid()](ppid.md) for the parent process and [$execctl()](execctl.md) to inspect subprocesses managed by [/exec](../commands/exec.md).

## Practical

Use the client's process ID when identifying it in operating-system tools or when a script needs to pass that ID to another operation, such as [$killpid()](killpid.md).

## Returns

The client's process ID as a decimal integer string. There are no function-specific error returns, output, or state changes, and no IRC connection is required.

## Examples

`/echo Client process: $pid()` displays the current client's process ID. The number depends on the running process.

## History

First appeared in ircII-2.2pre7.myc6.phone.

## Source

Registration and `function_pid()`: [source/functions.c](../../source/functions.c). Integer return conversion: [include/functions.h](../../include/functions.h).
