# ppid function

## Synopsis

`$ppid()`

## Technical

Returns the operating-system process ID of the EPIC client's parent process using `getppid()`. All arguments are ignored. Use [$pid()](pid.md) for the client's own process ID.

## Practical

Use this function when you need to identify the client's parent process, for example before passing that ID to [$killpid()](killpid.md). The parent is often the shell that launched the client, but it is not guaranteed to be a shell; the result identifies the operating-system parent, not an arbitrary subprocess started with [/exec](../commands/exec.md).

## Returns

The parent process ID as a decimal integer string. There are no function-specific error returns, output, or state changes. The function is always available and requires no IRC connection.

## Examples

`/echo Parent process: $ppid()` displays the client's parent process ID. The number depends on the running process.

## History

First appeared in ircII-2.2pre7.myc6.phone.

## Source

Registration and `function_ppid()`: [source/functions.c](../../source/functions.c). Integer return conversion: [include/functions.h](../../include/functions.h).
