# set insert_mode

## Synopsis

```text
/set insert_mode [on|off|toggle]
```

Default: `on`. With no value, [set](../commands/set.md) displays the current value. `toggle` reverses it; other values are rejected as invalid boolean settings.

## Description

Controls how ordinary character input behaves in the middle of an editable input line:

- `on`: insert before the character under the cursor, preserving the following text and shifting it right.
- `off`: overwrite the character under the cursor, preserving the text after that character.

At the end of the line, typing appends in either mode. Input is handled as Unicode code points encoded in UTF-8, rather than replacing a fixed number of bytes or terminal columns. A visual character composed of several code points is not necessarily one overwrite unit.

For example, with `hello` in the input line and the cursor on `e`, typing `X` produces `hXello` with insertion on, or `hXllo` with insertion off.

## Effects and controls

The setting is global across input screens. Changing it leaves existing input text and cursor positions unchanged; it affects subsequent ordinary character insertion and requests status updates. Other operations that replace or edit the input buffer can have their own behavior.

The [toggle_insert_mode](../bind/toggle_insert_mode.md) binding changes this same setting. Its built-in default binding is Tab/Control-I (`^I`); EPIC also installs the terminal's `key_ic` sequence when available, commonly the Insert key. Scripts can replace those bindings, so a configured Tab completion action need not toggle insertion.

The `%I` status field uses [status_insert](status_insert.md) when insertion is on; the overwrite field uses [status_overwrite](status_overwrite.md) when it is off. Visibility depends on the selected status format and ordinary status-window rules. The setting changes local editing, not IRC server modes, and sends no request.

## Example

```text
/set insert_mode off
/set insert_mode toggle
```

Select overwrite mode, then return to insertion. Use `/set insert_mode on` to select insertion explicitly.

## Source references

- [source/input.c](../../source/input.c): `input_add_character` inserts, appends, or replaces the current input code point; `toggle_insert_mode` changes the global setting.
- [source/keys.c](../../source/keys.c): action registration and default Tab and terminal Insert bindings.
- [source/status.c](../../source/status.c): insertion and overwrite status fields read the setting.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration, status-update callback, and default on.
