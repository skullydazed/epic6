# $exec()

## Synopsis

`$exec(<program> [<args> ...])`

## Technical

`$exec()` forks a program and connects pipes to its standard input, standard output, and standard error. The first word selects the executable; remaining words become its arguments. Double quotes group an argument containing spaces and are removed before execution. This parsing always recognizes double quotes, independently of the word settings used by other functions.

The program runs directly, without an implicit shell. Shell pipelines, redirection, wildcard expansion, and shell variables therefore have no special meaning. To request shell interpretation explicitly, use `$exec(sh -c "<shell command>")`, allowing for EPIC's own expansion of the function arguments first. An executable without a directory component is searched for using `PATH`; the child inherits the current directory and environment. A leading `~` is not expanded into a home directory.

The returned handles work with [$read()](read.md), [$write()](write.md), [$writeb()](writeb.md), and [$close()](close.md). `$write()` appends a newline and flushes its output; `$read()` reads a line and removes its final newline. Close each handle when finished. Closing the input handle tells the program that no more input will arrive.

## Practical

This function offers direct control over a program's input and output, for example when implementing a text filter. The [/exec command](../commands/exec.md) is usually easier for interactive subprocesses because it integrates their output with EPIC's event loop.

These pipes are blocking. Writing can block when the program is not consuming its input; reading can block until the program produces a complete line or closes its output. The program's own output buffering can delay a response even after EPIC has flushed its input. If EPIC and the program are both waiting for each other, they deadlock. Understand the program's input, output, and buffering behavior before using it as a filter, and account for both standard output and standard error.

## Returns

On successful pipe setup, three space-separated EPIC file handles, in this order:

1. A writable handle for the program's standard input.
2. A readable handle for its standard output.
3. A readable handle for its standard error.

These are handles in EPIC's file registry, rather than operating system file descriptors or a process ID. Missing input or a pipe, fork, or stream setup failure returns the empty string. Setup failures also print a diagnostic.

## Restrictions

Successful setup does not establish that the executable started successfully: the child exits without an execution-failure diagnostic if it cannot execute the program. The function does not return an exit status or register the child in the `/exec` process list. Closing its handles does not wait for it to finish, and `/wait` process references do not identify this child.

Blocked reads or writes prevent EPIC from servicing its normal event loop. Checking [$eof()](eof.md) does not tell you whether a read will block. Draining one output stream while ignoring the other can deadlock if the ignored stream fills. Closing an unread output stream can instead cause the program's later writes to fail.

## Examples

For a small, predictable command that writes one line to standard output and requires no input:

```irc
fe ($exec(echo hello)) in out err {
    @ close($in)
    echo $read($out)
    @ close($out)
    @ close($err)
    break
}
```

This prints `hello` when an external `echo` executable is available through `PATH`. It is not a general pattern for programs with substantial output or error output.

## Source

Registration and argument parsing: [source/functions.c](../../source/functions.c). File-handle registration and stream operations: [source/files.c](../../source/files.c). Pipe creation and direct execution: [source/ircaux.c](../../source/ircaux.c). The separate `/exec` process tracking is in [source/exec.c](../../source/exec.c).
