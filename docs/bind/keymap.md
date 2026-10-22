# keymap

## Synopsis

```text
/bind <sequence> keymap <map-name>
```

## Description

Select an existing keymap for subsequent keyboard input. The stored argument text is used as the map name, case-insensitively; the action does not create a map. An unknown name silently selects `HEAD`, the normal input map. With no argument, the action leaves the current map unchanged.

EPIC initializes `HEAD` and `MOUSE`. The mouse map consumes terminal mouse report characters through [mousemachine](mousemachine.md). Changing maps persists until another action changes the map; it is not limited to the next key. Binding inspection and changes through [bind](../commands/bind.md) also use the current map.

Enabling [mouse](../set/mouse.md) binds the mouse report prefix `^[[<` in `HEAD` to `keymap MOUSE`; disabling it removes that prefix binding. Completed mouse reports return input to `HEAD`.

## Example

```text
/bind ^[k keymap HEAD
```

Press Escape followed by lowercase `k` to select the normal input map, provided that sequence is available in the map receiving input.

## Source references

- [source/keys.c](../../source/keys.c): `switch_keymap`, `switch_keymap_kb`, `init_keys`, `init_mousemachine`, and `set_mouse_support`.
