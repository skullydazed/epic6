# $outputinfo()

## Synopsis

`$outputinfo()`

## Technical

Returns the current output context's level name and, when present, its routing target. This context helps EPIC decide where output belongs. Arguments are ignored.

The first field is the canonical name of the current output/lastlog level, such as `MSGS`, `PUBLICS`, or `OTHER`. The optional remainder is the context's target, commonly a nickname or channel. For an incoming private message this is normally the sender's nickname; for a channel message it is normally the channel. It is not necessarily the separate sender field of the context.

When the target pointer is unset, only the level name is returned. When it is set, one space and its literal text are appended without quoting. An explicitly empty target therefore leaves a trailing space. The function does not return the server, explicit destination window, or suppression state.

## Practical

Use this to inspect inherited output context in a hook or script. [$querywin()](querywin.md), [$chanwin()](chanwin.md), and [$levelwindow()](levelwindow.md) can help inspect related routing, but these two returned fields are not sufficient to predict every destination. Explicit window selection, server context, current-window level routing, and fallback rules also affect output.

[/echo](../commands/echo.md) inherits the current output context; [/xecho](../commands/xecho.md) can create a different one. A nonempty result does not mean output is enabled or visible. Suppressed contexts still have level and target information; [$isdisplaying()](isdisplaying.md) separately reports the display flag, which is also distinct from context suppression.

## Returns

The level name, optionally followed by a space and the routing target. The call prints nothing and changes no context or window state.

## Examples

```irc
on msg "hop *" { eval echo $outputinfo() }
on public "% #epic *" { eval echo $outputinfo() }
```

In the normal incoming-message contexts, the first handler reports `MSGS hop`, and the second reports `PUBLICS #epic`. A miscellaneous context with no target returns `OTHER`, rather than the older alias `CRAP`. Nested commands that establish another context can change the value observed inside them.

## History

This function first appeared in EPIC4 1.1.12.

Registration and `function_outputinfo()`: [source/functions.c](../../source/functions.c). Context fields and getters: [source/window.c](../../source/window.c). Routing priorities: [source/screen.c](../../source/screen.c). Incoming-message contexts: [source/parse.c](../../source/parse.c). Canonical level names: [source/levels.c](../../source/levels.c).
