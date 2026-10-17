# on redirect (removed)

## Former synopsis

```text
/on [<noise>]redirect "<pattern>" { <commands> }
```

## Description

The `redirect` event formerly reported text about to be redirected to another target. It supplied the destination in `$0` and the text in `$1-`.

EPIC6 has removed this named hook. There is no built-in redirect event through which a script can observe, replace, or suppress text using those historical fields.

## Compatibility

Attempting to register `/on redirect ...` is rejected with `No such ON function: REDIRECT`. The current built-in command table also has no `redirect` command; scripts can define their own commands, but that does not restore this named hook or its former dispatch path.

Some current interfaces still use the word redirect for other metadata or routing behavior. For example, exec records expose a `redirect` field, and exec output handling checks it when deciding whether to display a line locally. That does not invoke `on redirect`, and the current exec reader does not automatically forward those lines to IRC.

For process-output handling, use [on exec](../on/exec.md), [on exec_errors](../on/exec_errors.md), or the process's callbacks as appropriate. For observing particular outgoing IRC message classes, use their relevant send events. These have their own arguments and suppression rules rather than the former redirect contract.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records removal of the previously sunset redirect hook.
- [source/hook.c](../../source/hook.c): the named-hook table omits `redirect`; `find_hook` rejects unknown event names.
- [include/hook.h](../../include/hook.h): current hook identifiers contain no redirect event.
- [source/commands.c](../../source/commands.c): the built-in command table contains no `redirect` command.
- [source/exec.c](../../source/exec.c): exec records retain redirect metadata, but `handle_filedesc` uses it only to gate default local display and does not invoke a redirect hook.
