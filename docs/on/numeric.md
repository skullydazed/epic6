# on numeric

## Synopsis

```text
/on [<noise>]numeric "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when a numeric reply reaches EPIC's common numeric-display stage, after the corresponding numbered hook permits default handling. It provides one place to customize many numeric replies without registering each numbered event separately.

It is not an unconditional event for every numeric received. Some replies are consumed by protocol handlers, request callbacks, or earlier specialized events before reaching this stage. An earlier suppressing [on raw_irc](raw_irc.md) hook can also prevent numeric processing entirely.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the numeric value, reporting server, and remaining reply fields together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Numeric value, formatted as a decimal integer without leading zero padding. |
| `$1` | Sender of the numeric, normally the reporting server. |
| `$2-` | Remaining reply parameters joined with spaces, excluding the initial recipient nickname. |

For numeric `001`, `$0` is `1`, not `001`. The remaining message can contain multiple words and fields; `$2` alone is only its first word. IRC framing, the initial recipient, and trailing-parameter colon are not preserved as a raw protocol line. Earlier numeric-specific processing can alter the argument text; for example, the list-start reply supplies synthetic `Channel Users Topic` fields.

Server numeric meanings and field layouts vary. This hook does not normalize them into a common schema beyond the leading numeric and sender fields.

## Default behavior and ordering

The usual order is numeric-specific state processing, the numbered hook, `numeric`, and then built-in display. For example, a suppressing `on ^322` hook prevents this event for that list row. Some numerics also have earlier named hooks or specialized request handling and can stop before the common stage.

If this hook permits default handling, EPIC continues its numeric-specific display branch or generic reply display. A suppressing hook, such as a serial-zero `^numeric` hook, skips that later branch for the reply. Those branches can include further named events, such as [on names](names.md), as well as display, so suppression can prevent more than one line of formatting. It does not undo state processing that already occurred, cancel the server's operation, or suppress later replies.

The usual output level is `other`, with a channel context when the first remaining parameter identifies one, although individual numeric handlers can change routing. Handling this event does not automatically send an IRC request.

## Example

Replace display of list rows that reach the common stage:

```text
on ^numeric "322 *" (code, server, fields) {
    echo List row from $server: $fields;
};
```

An earlier suppressing `322` hook or [on list](list.md) hook can prevent this handler from running.

## History

This hook first appeared in EPIC5-0.3.1.

## Source references

- [source/numbers.c](../../source/numbers.c): `numbered_command` removes the recipient field, performs numeric-specific handling, offers numbered hooks, invokes `numeric`, and conditionally executes display branches.
- [source/parse.c](../../source/parse.c): `parse_server` invokes `raw_irc` before dispatching numeric replies.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
