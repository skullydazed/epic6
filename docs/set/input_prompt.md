# set input_prompt

## Synopsis

```text
/set input_prompt [<string>]
/set -input_prompt
```

Default: `> ` (a greater-than sign followed by a space). With no string, [set](../commands/set.md) displays the current value. `/set -input_prompt` disables the ordinary prompt. The string can contain spaces.

## Description

Adds a custom display prefix to the input line. It is not part of the editable input buffer, cannot be backspaced over, and is not sent with a submitted command or message. It is commonly used to display information about the current channel or query target.

Variables and functions in the stored prompt are expanded whenever EPIC updates the input display, not merely after a command is submitted. Expansion uses an empty argument string, so `$*` is not the text currently being typed. Preserve literal dollar signs when assigning a dynamic prompt if normal command parsing would otherwise expand them at assignment time.

The expanded text is normalized through the normal display-mangling path. Formatting controls such as bold and color can be used, subject to those display rules. EPIC measures its display width and redraws the input line when the resulting prompt changes; a long prompt leaves less room for editable text.

## Scope and effects

Changing or unsetting the setting updates the global default prompt and requests an input redraw for all screens. Each screen's input line can have its own raw prompt, such as a temporary prompt used while collecting input; that takes precedence over this default. Unsetting the global setting does not remove such a separate prompt.

The ordinary input display is updated only in full-screen mode while the client is in the foreground. This setting does not provide a shell prompt or change an external program's input. It sends no IRC request.

## Example

At the interactive command line, store a prompt that follows the current target:

```text
/set input_prompt $$T> 
```

The doubled dollar sign preserves `$T` during assignment so the target is evaluated during later prompt redraws. The example ends with a space to separate the prompt from typed text. Additional quoting may be needed when embedded in another layer of script expansion.

Disable the ordinary prompt with:

```text
/set -input_prompt
```

## Source references

- [source/input.c](../../source/input.c): `set_input_prompt` stores the global default and requests redraws; input display processing selects a raw per-input-line prompt first, expands and normalizes it, and measures its width.
- [source/vars.c](../../source/vars.c): string registration and prompt callback.
- [include/config.h](../../include/config.h): default `> `.
- [source/expr.c](../../source/expr.c): `$$` preserves a literal dollar sign for later expansion.
