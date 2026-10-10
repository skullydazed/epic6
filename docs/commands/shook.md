# shook

## Synopsis

```text
/shook <on-type> [<args>]
```

## Description

`shook` raises a synthetic event for the specified [on](on.md) type. It passes the remaining argument text to EPIC's normal hook dispatcher, allowing matching script handlers to run as though that hook had been raised. It does not suppress or disable hooks.

The event type is case-insensitive. An exact name or unambiguous name prefix is accepted, as is a numeric hook number in the supported range. Missing, unknown, ambiguous, or out-of-range types print a diagnostic. No options or noise prefixes are accepted on the type argument; those belong to the installed `on` handlers.

The remaining text is the complete event data. Use the fields expected by that event type: for `msg`, for example, a sender followed by message text. The handler receives the text through its positional arguments and `$*`, or its declared argument list. `shook` does not validate the number or meaning of these fields. Omitting them raises the event with empty data.

Normal hook pattern matching, serial numbers, noise levels, excepting patterns, implied hooks, and recursion restrictions apply. Hooks disabled through [hookctl](../functions/hookctl.md) remain disabled. The command discards the dispatcher's result, so a handler's decision about suppressing a default action has no following built-in event action to control. Handler output, activation notices, or an implied hook can still display text; without those, a synthetic `msg` does not automatically print a received message. There is no script return value.

## Restrictions

Be careful when synthesizing events normally raised by server messages. `shook` does not parse an IRC line or establish its sender's userhost, server, output context, channel state, or other event metadata. A [userhost](../functions/userhost.md) result is not populated for the synthetic sender merely because their nickname appears in the arguments; existing context may still reflect another event. Handlers that rely on such metadata can behave incorrectly.

The command does not require an IRC connection. It does not itself receive or transmit a message or update IRC state, although the scripts it invokes can perform those actions. To pass a simulated IRC line through the client parser, see [pretend](pretend.md), which has broader effects.

## Examples

```text
on ^msg "Me *" {echo Synthetic message from $0: $1-}
shook msg Me This message is fake
```

The installed handler displays `Synthetic message from Me: This message is fake`. It remains installed and can also match subsequent real messages from `Me`; the output comes from the handler's `echo`, not automatic message display by `shook`.

## Source

Registration is in [source/commands.c](../../source/commands.c). `shookcmd()`, event-type lookup, and dispatch through `do_hook()` are in [source/hook.c](../../source/hook.c). Real server-message metadata is established separately in [source/parse.c](../../source/parse.c).
