# Event-driven programming

Event-driven programming organizes a program around waiting for activity and handling it. EPIC's core loop waits for work, processes it, and continues until the client exits. In scripts, an event is a named opportunity to inspect or customize some part of that processing.

## Things happen

Typical sources of activity include:

- Keyboard input.
- Network input, such as a server message.
- Timer expiration and periodic work.

When there is no immediate work, EPIC can wait in the operating system instead of repeatedly checking in a busy loop. A wakeup can also result from a signal or interrupted wait; it does not necessarily correspond to exactly one script-visible event.

## The event loop handles activity

EPIC calculates the next timer timeout, waits for descriptor activity or that timeout, and dispatches input or timer callbacks. It also performs housekeeping and display updates. “Event loop” describes this repeating process, not a promise that the client is asleep at every instant outside an event handler.

Scripts usually register callbacks and return. EPIC calls them when relevant activity occurs. A long-running callback or loop can delay normal client work, so scripts should finish their immediate task and leave later work to the appropriate callback or timer.

## Script events describe what happened

For example, receiving an IRC private message involves reading network input, parsing the IRC message, processing CTCP content, and classifying the destination. An ordinary private message addressed to your nickname can then produce the [`MSG`](../on/msg.md) event.

`MSG` is the opportunity to handle that received message, rather than the act of displaying it. EPIC normally displays `*sender* message` afterward if the hook result permits default handling. Earlier processing or hooks can prevent a particular input from reaching `MSG`; not every network read produces one.

## Extend or replace handling with ON

The [`on`](../commands/on.md) command registers a handler, also called a hook. Supply an event type, a pattern matching that event's argument string, and code to run. For example:

```irc
on ^msg "*" {
    echo msg from $0: $1-;
};
```

This replaces the normal private-message display with a line supplied by the handler. It does not send a reply or prevent receipt of the message.

## Unpacking an ON definition

In the example:

- `msg` selects the event type.
- `^` selects a suppressing noise mode. At the default serial number, zero, the selected hook suppresses the event's default display.
- `"*"` matches all argument strings for that event.
- The braces contain the commands executed for the selected event.
- `$0` is the sender, and `$1-` is the remaining message text.

The event's reference page defines its fields; other events use different layouts. A named argument list can make the same code easier to read:

```irc
on ^msg "*" (sender, message) {
    echo msg from $sender: $message;
};
```

The final named parameter receives the remaining text, preserving a message with spaces. Named parameters are local variables and consume their values from the handler's positional argument string. See [argument lists](arglist.md).

## Selecting a handler and its default action

For each serial-number group, EPIC chooses the matching pattern with the highest wildcard score. Separate serial groups allow independent callbacks; two matching hooks at the same serial are alternatives rather than two actions that always run. See [serial numbers](serial_numbers.md).

The noise mode determines ordinary callback display behavior and, at serial zero, whether to suppress the caller's default action. For a display replacement, `^` is appropriate. A quiet `-` handler can add processing while allowing the usual display. Suppression is interpreted by the particular event's caller; it does not universally cancel the underlying operation or undo state already updated.

## Calling and finishing a callback

A normal ON callback runs as a named script call with a local-variable scope. EPIC supplies the event arguments and binds any declared parameters before executing the body. The script can run commands, evaluate expressions, and register or modify later callbacks under their normal rules.

When the callback returns, its local scope is destroyed, the hook engine restores its display context, and any other selected serial groups can run. Once hook processing finishes, the event's caller decides whether to perform its default action. EPIC then continues the surrounding input processing and event loop.

This is usually sequential execution, not a separate thread for every callback. Some waiting commands re-enter the event loop while an outer script call is suspended, allowing other activity before that call resumes. A local scope therefore controls variable visibility and lifetime; it does not make a script transaction atomic. See [atomic scopes](atomic_scope.md) and the documentation for any waiting command you use.

## Source references

- [source/irc.c](../../source/irc.c): `io()` computes timeouts, waits, dispatches descriptors/timers, and performs housekeeping.
- [source/parse.c](../../source/parse.c): `p_privmsg()` classifies messages, invokes `GENERAL_PRIVMSG` and `MSG`, and applies default display.
- [source/hook.c](../../source/hook.c): handler selection, serial processing, noise modes, and callback invocation.
- [source/commands.c](../../source/commands.c): named callback scopes and argument binding.
- [source/server.c](../../source/server.c): `server_hard_wait()` demonstrates event-loop re-entry during a waiting operation.
