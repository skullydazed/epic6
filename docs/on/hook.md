# on hook

## Synopsis

```text
/on [<noise>]hook "<pattern>" [(<argument list>)] { <commands> }
/hook <text>
```

## Description

Triggered when the [hook](../commands/hook.md) command is issued with nonempty text. This is a script-controlled event: it is useful for dispatching custom notifications through the normal hook matching system instead of calling an alias directly.

It can help organize code that would otherwise recurse through aliases, but it is not an automatic recursion guard. The `hook` event permits nested invocation. A handler that repeatedly issues matching `/hook` commands can recurse, so arrange your patterns and handler actions to terminate.

Use the standard [on](../commands/on.md) pattern, serial-number, and noise controls. The pattern matches the entire supplied text; there are no event-specific options or required application field names.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$*` | Text supplied to `/hook`. |
| `$0`, `$1`, and so on | Words of that text, according to ordinary hook argument handling. |

A script can choose a leading word as an event name and use later words as payload. An argument list can assign names to those fields. `/hook` with no text prints `Usage: /HOOK [text]` without generating the event.

## Default behavior

There is no built-in action or output when the event is unhandled. The command ignores the hook's default-handling return value, so suppression does not cancel a separate action after `/hook`. Matching handlers still run according to the normal hook system.

The event is synchronous: handlers run before the invoking `hook` command returns. It has no dedicated server request or output-window context; handler commands use the context in which it was invoked unless they explicitly choose another.

## Example

Define and trigger a custom script notification:

```text
on ^hook "project_saved *" (event, filename) {
    echo Saved $filename;
};
hook project_saved notes.txt;
```

Other scripts can register handlers using their own serial numbers and patterns. The first word is an application convention, not a built-in event subtype.

## Source references

- [source/commands.c](../../source/commands.c): `hookcmd` rejects empty text and invokes the hook without a default action.
- [source/hook.c](../../source/hook.c): the `hook` registration has no nonrecursion flag; `do_hook_internal` performs standard matching and synchronous handler execution.
