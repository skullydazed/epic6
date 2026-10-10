# defer command

## Synopsis

```text
/defer {<commands>}
/defer <command-text>
```

## Description

`defer` queues commands to run later, after the current event's script processing returns to the outer event loop. It is useful when changing or deleting something while another active command or hook may still be using it.

For example, deleting a window, server record, hook, or open file from inside a callback can create sequencing problems for code that has not finished running. Deferring the change lets the current callback finish first. This reduces that risk; it does not guarantee that every queued operation is safe or that its target will still exist when it runs.

A deferred operation is not immediate. Do not use it where the operation must already have finished before the next statement executes. It is also not a timer: there is no delay argument or promise of a particular elapsed time.

The command has no options, produces no confirmation, and queues all supplied argument text. With no text, it queues an empty operation. It is available in every build and does not itself require an IRC connection.

## Execution context and ordering

EPIC saves the command text, current server reference, and current positional argument text. At dispatch, it selects that server's current window and runs the text in a new local scope named `deferred`. It then restores the prior server/window context after processing the queue. The original window is not captured separately, and the original local variables are not retained.

Braces keep expansion of the enclosed text for execution time. Without braces, ordinary `$` expansion can occur before `defer` receives the text; the deferred execution parses that stored text again. `$*` inside a deferred block uses the saved positional argument text, but ordinary local variables from the scheduling scope are unavailable. Use a global variable or expand the needed value into the queued text deliberately.

Entries run in queue order during housekeeping at the outermost `io()` event-loop level. Nested event loops, such as those used by a blocking wait, do not dispatch the queue themselves. A deferred command that queues more work appends it to the same queue; the dispatcher continues through appended entries. Repeatedly deferring itself can therefore prevent the pass from finishing.

There is no list, cancellation, or returned-result interface in this command. Errors from the queued commands occur when they execute, not when they are scheduled. Server/window state and other referenced resources can change between those times.

## Example 1: changing a hook later

Instead of removing a set of numeric hooks while one of them is being dispatched, queue that removal:

```text
on #401 -1 "*" { defer { on 401 - } }
on ^401 "*" { echo -- 401 hooked! $* }
```

The queued removal does not stop other hooks selected for the current event from running first. This demonstrates the sequencing purpose, rather than claiming that removing a hook immediately always causes a crash.

## Example 2: deferred output and window operations

In a script:

```text
defer { echo This runs later }
echo This runs first
```

For operations inside window-event hooks, consider deferring changes to windows until the active hook has finished. Check which window will be current at dispatch; a deferred operation does not automatically target the window that scheduled it.

## History

The `defer` command first appeared in EPIC4-0.9.16.

## Source

See [source/commands.c](../../source/commands.c): `defercmd()`, `do_defered_commands()`, and the lambda-command execution helpers. [source/irc.c](../../source/irc.c) dispatches queued work only at the outermost `io()` level. [source/expr.c](../../source/expr.c) implements command expansion and brace protection; [source/alias.c](../../source/alias.c) implements local scopes.
