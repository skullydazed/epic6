# hook

Generate an `on hook` event for your scripts.

## Synopsis

```text
/hook text
```

## Description

`hook` passes the entire supplied text to the `hook` event. Register handlers with [on](on.md) to create your own script events. A conventional first word such as `foo` is simply part of the text matched by those handlers; it does not select a different built-in event type.

Matching follows the normal `on` rules: wildcard patterns are matched against the event text, and the best matching handler in each serial-number group is selected. A handler receives that text as its argument context, so `$*` is the whole event text and `$0` is its first word. Normal hook noise and display controls apply, so handlers may print activation notices or their own output.

The command has no default echo or IRC request, and ignores the hook's suppression result. With no matching handler or configured implied hook, it produces no output. An implied hook can provide output through the normal hook machinery. Supplying no text prints a usage message. There are no options, and the built-in is always registered.

The `hook` event allows recursion by default. A handler that calls `hook` again runs the next event immediately; use a [timer](timer.md) or another scheduling command if you want delayed repetition. Global hook suppression also affects these events.

## Examples

Create and invoke a simple script event:

```text
on ^hook "greeting *" {
    echo Hello, $1-
}
hook greeting world
```

Schedule a line every ten seconds:

```text
on ^hook "foo" {
    timer 10 {
        echo This line is repeated every 10 seconds
        hook foo
    }
}
hook foo
```

The timer and handler create this repetition; `hook` itself does not repeat or schedule anything. Each timer invocation schedules the next one through the handler.

## Source

The registration and `hookcmd` implementation are in [source/commands.c](../../source/commands.c). The event declaration, matching, callback argument context, noise controls, and implied-hook behavior are in [source/hook.c](../../source/hook.c).
