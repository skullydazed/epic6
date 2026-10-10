# echo command

## Synopsis

```text
/echo [text]
```

## Description

`echo` prints its argument text through EPIC's normal local output system. It is useful for script status messages and for replacing the display of server messages inside hooks. With no text, it outputs an empty line.

There are no options: dash-prefixed words are text, not flags. The handler does not add a banner or interpret percent signs as formatting placeholders. To select a window, level, banner, or other output option explicitly, use [`xecho`](xecho.md).

`echo` temporarily enables window display while outputting the text, then restores the previous display state. It therefore still produces output when `set display` is off. This does not bypass all output controls: normal output suppression and a [`window`](../on/window.md) hook can prevent display.

The command does not select a new output context. Window routing, output level, and target are inherited from where it is called, so an echo inside a hook may follow that hook's routing rather than always going to the current visible window. Normal output rewriting, display control-character handling, line wrapping, scrollback, lastlog, and enabled logs apply. Held or hidden windows can retain output without showing it immediately.

The command is available in every build, requires no IRC connection, and sends no IRC message by itself.

## Expansion and length

The handler prints the text it receives; it does not expand variables itself. Direct interactive input normally reaches it without `$` expansion. Inside an alias or other script context that expands commands, variables are expanded before `echo` runs. Use [`eval`](eval.md) for an additional parsing/expansion pass when needed.

The historical `input_aliases` setting is not registered in the current source, so it is not a supported way to enable expansion here. Input hooks supplied by scripts may implement their own processing.

Unlike printf-style output helpers, this handler passes the argument string directly to `put_echo()` and has no fixed formatting-buffer limit of its own. Input, parser, memory, and downstream display/logging limits can still apply; this is not a promise of unlimited output.

## Examples

To display a local status message:

```text
/echo Finished loading my settings
```

To expand a variable through an explicit evaluation pass at the input prompt:

```text
/eval echo Current nickname: $N
```

In a script alias:

```text
alias report {
    echo Received arguments: $*
}
```

Calling `/report one two` displays `Received arguments: one two` through the current output context.

## Source

See [source/commands.c](../../source/commands.c) for `echocmd()`, `evalcmd()`, and pre-dispatch expansion. [source/output.c](../../source/output.c) implements `put_echo()` and session logging. [source/screen.c](../../source/screen.c) implements output suppression, hooks, routing, rewriting, and window display. [source/input.c](../../source/input.c) processes ordinary interactive input without command expansion; [source/vars.c](../../source/vars.c) registers the current settings.
