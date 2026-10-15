# toggle_insert_mode

## Synopsis

```text
/bind <key> toggle_insert_mode
```

## Description

Toggle the client's input mode between insert and overwrite by changing the global [insert_mode](../set/insert_mode.md) setting. This built-in action is equivalent to setting `insert_mode` to `toggle`; it affects subsequent character insertion on all input screens.

In insert mode, typing inserts before the character under the cursor, shifting the following text right and preserving it. In overwrite mode, typing replaces the character under the cursor. At the end of the input line, typing appends text in either mode. Insert mode is on by default.

The action leaves existing input text and the cursor position unchanged. It requests status updates and reports the new setting through the normal setting-value display. It takes no arguments or options; extra text supplied with the binding is ignored. It sends no IRC request.

The default binding is Tab/Control-I (`^I`). EPIC also binds the terminal's `key_ic` sequence when available, normally the Insert key. Scripts can change these bindings, so Tab may instead perform completion in a scripted setup.

## Example

```text
/bind ^I toggle_insert_mode
```

With `hello` in the input line and the cursor on the `e`, typing `X` in insert mode produces `hXello`. Typing `X` in overwrite mode instead produces `hXllo`.

See [bind](../commands/bind.md) for key sequence syntax and [self_insert](self_insert.md) for character insertion.

## Source references

- [source/keys.c](../../source/keys.c): action registration, default Control-I binding, and terminal Insert binding.
- [source/input.c](../../source/input.c): `toggle_insert_mode` calls `set_var_value` with `toggle`; `input_add_character` reads the setting when inserting characters.
- [source/vars.c](../../source/vars.c): boolean toggling, setting-value output, and the status-update callback.
- [include/config.h](../../include/config.h): default insert mode is on.
