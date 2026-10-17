# on wall

## Removed event

The historical `wall` hook was associated with incoming IRC `wall` messages. That protocol event had already become a relic; EPIC6 removes its dedicated hook registration and has no incoming `wall` handler.

There are no supported arguments, default output, or automatic notifications for the former event. Do not use an old `on wall` definition expecting to receive historical wall messages.

## Current alternatives

Use [on wallop](../on/wallop.md) for incoming `wallops` messages. [On operwall](../on/operwall.md) handles the special `OPERWALL - ` prefix within those messages. These are separate events with their own arguments and suppression behavior.

The [on](../commands/on.md) command accepts unambiguous event-name abbreviations. Because `wallop` remains registered, `on wall` currently resolves as an abbreviation of `on wallop`; accepting that spelling does **not** restore the removed event. Spell `wallop` in full when that is the event you intend to handle.

For example, observe current wallops with their actual arguments:

```text
on -wallop "*" (sender, kind, text) {
    echo Wallop from $sender ($kind): $text;
};
```

The `kind` is `S` when the sender contains a dot, otherwise `*`. Whether a server sends such messages depends on its protocol and configuration.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records removal of the sunset `wall` event.
- [source/hook.c](../../source/hook.c): registers `wallop` but no dedicated `wall` event; `find_hook` accepts unambiguous abbreviations.
- [source/parse.c](../../source/parse.c): protocol dispatch has `wallops` but no `wall` handler; `p_wallops` invokes `operwall` and `wallop`.
