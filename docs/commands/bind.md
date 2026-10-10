# bind command

## Synopsis

```text
/bind
/bind -defaults
/bind <sequence>
/bind -recursive <sequence>
/bind <sequence> <action> [arguments]
/bind -symbolic <terminal-capability> [<action> [arguments]]
```

## Description

`bind` controls what EPIC does when you type a sequence of one or more keys. EPIC provides useful defaults, so you generally need this command only to change a key's behavior.

With no arguments, it displays bindings in the current keymap, omitting ordinary `self_insert` entries. With a sequence but no action, it displays that sequence's binding. With an action, it replaces the binding and stores any remaining argument text for the action. Binding changes are local to the client, are available in every build, and do not require an IRC connection.

Sequences are case-sensitive. Action names are case-insensitive and must name a registered binding action, not simply any command or alias. An unknown action reports `No such function` and leaves the existing binding unchanged. Use `parse_command` to run command text, or see [`bindctl`](../functions/bindctl.md) for registering actions backed by aliases.

The special action `nothing` removes the binding for that exact sequence; longer sequences beginning with it can remain bound. Printable single characters normally use `self_insert`. Avoid replacing them unless you want to change how text is entered.

## Options

| Option | Effect |
| --- | --- |
| `-defaults` | Discard current custom bindings and registered actions, then recreate built-in bindings and terminal-derived defaults. |
| `-recursive` | When inspecting a sequence, also display bindings beneath that prefix. With no sequence, display the current map. |
| `-symbolic` | Look up a terminal capability by its long name, such as `key_right`, and use its sequence instead of literal key notation. |

Options are case-insensitive and recognized by their first letter (`-d`, `-r`, or `-s`). Only the first token is checked for an option. `-recursive` does not change the behavior of a supplied assignment. An unrecognized dash-prefixed token is treated as a sequence, rather than rejected as an option. A missing symbolic name makes no change; an unavailable capability reports that it is unsupported in your terminal type.

Use the long capability names supported by EPIC's terminal table. Their values depend on the current terminal description; see your system's terminfo documentation. A symbolic capability can also be inspected without supplying an action.

## Sequences

You may enter printable notation for control characters. The sequence argument supports double quoting, which is useful for a sequence containing a space.

| Byte | Notation |
| --- | --- |
| 0 | `^@` |
| 1–26 | `^A` through `^Z`, or lowercase equivalents |
| 27, escape | `^[` or `\e` |
| 28 | `^\` |
| 29 | `^]` |
| 30 | `^^` |
| 31 | `^_` |
| 92, backslash | `\\` |
| 94, caret | `\^` |
| 127, delete | `^?` |
| Octal byte | `\nnn`, using one to three octal digits |

Octal escapes are reduced to their low seven bits: `\200` becomes byte 0, not byte 128. Literal bytes with the high bit set cannot be bound. Input characters above 127 are handled as `self_insert`, independently of these bindings.

Escape literal backslashes and carets as shown to avoid accidental notation. Other backslash escapes simply remove the backslash; invalid caret notation is treated literally. An empty sequence is rejected. EPIC's command or script parser can also process escapes before the binding parser receives them.

## Overlapping sequences and input limits

Sequences can overlap: escape (`^[`) can have one action while an arrow sequence such as `^[[A` has another. EPIC waits for more input at an ambiguous prefix and chooses longer matches when possible. The `key_interval` setting controls the timeout in milliseconds; its configured default is 1000, and values below 10 are raised to 10.

When an incomplete or unmatched sequence cannot resolve, EPIC can backtrack and execute shorter component bindings. An individually unbound key is ignored; an unmatched multikey sequence is not necessarily discarded as a whole.

Terminal or operating-system input handling can intercept keys before EPIC sees them, such as control-S and control-Q when flow control is active. A binding cannot make an intercepted key available to the client.

A [`keybinding`](../on/keybinding.md) hook can suppress the action when the key is pressed. See [`rbind`](rbind.md) to find sequences bound to a particular action.

## Action arguments

`parse_command` executes its stored argument text as EPIC commands when the sequence is pressed. It is not the only action that uses arguments: `keymap`, for example, uses its argument to select a keymap. Other actions may ignore the text. Alias-backed actions append it to the registered alias invocation. Argument interpretation belongs to the action, not `bind`.

## Examples

To bind control-Q to insert the next character literally, if the terminal passes control-Q through:

```text
/bind ^Q quote_character
```

To bind the right arrow using a literal sequence, or using the terminal's symbolic name:

```text
/bind ^[[C forward_character
/bind -symbolic key_right forward_character
```

To execute a local command when escape followed by `x` is pressed:

```text
/bind ^[x parse_command {echo Shortcut pressed}
```

To inspect that binding, remove it, or restore defaults:

```text
/bind ^[x
/bind ^[x nothing
/bind -defaults
```

## Source

See [source/keys.c](../../source/keys.c): `bindcmd()`, sequence compression, binding validation, key dispatch and backtracking, key timeouts, and default initialization. [source/input.c](../../source/input.c) implements `parse_text()` for `parse_command`; [source/term.c](../../source/term.c) resolves symbolic capabilities. [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h) define `key_interval` and its default.
