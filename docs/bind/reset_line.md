# reset_line

## Synopsis

```text
/bind <key-sequence> reset_line [<new-text>]
```

## Description

Replace the current contents of the input line with `<new-text>` and place the cursor at its end. With no replacement text, clear the input line and place the cursor at its beginning, after the input prompt.

The replacement is literal text, like text inserted with [xtype](../commands/xtype.md) `-l`: its characters are not dispatched through their keybindings. `reset_line` replaces the whole line regardless of cursor position or insert/overwrite mode. The action itself does not expand variables or execute commands in the replacement text; normal parsing of the command that installs or invokes the action can still perform expansion.

This operation does not change the cut buffer. The previous contents of the input line are discarded without being saved by this action. The input display is redrawn, and the replacement is not submitted or sent to an IRC server.

This built-in action has no options or default key assignment. Everything after the action name is replacement text.

## Limitation

Replacement text is copied into the existing input buffer without expanding that buffer. Text that exceeds its current byte capacity minus the terminating NUL is silently truncated; truncation can split a multibyte UTF-8 character. The current buffer size can have grown through earlier typing, so there is no single fixed replacement length for every session.

## Examples

```text
/bind ^X^R reset_line hello
```

Press Control-X followed by Control-R to replace the line with `hello` and move the cursor after the `o`.

Scripts can invoke the same action directly with [parsekey](../commands/parsekey.md):

```text
/parsekey reset_line replacement text
/parsekey reset_line
```

The first command replaces the input with `replacement text`; the second clears it. Both preserve the cut buffer.

See [bind](../commands/bind.md) for key sequence syntax and [erase_line](erase_line.md) for clearing that saves text in the cut buffer.

## Source references

- [source/keys.c](../../source/keys.c): action registration, stored binding text, and `parsekeycmd` invocation.
- [source/input.c](../../source/input.c): `input_reset_line`, `set_input`, and `input_expand` implement replacement, cursor placement, and input-buffer capacity.
- [source/commands.c](../../source/commands.c): `xtypecmd` implements literal character insertion for comparison.
