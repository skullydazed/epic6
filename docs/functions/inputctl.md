# inputctl function

## Synopsis

`$inputctl(get cutbuffer)`

`$inputctl(set cutbuffer [<text>])`

## Technical

Queries or replaces EPIC's input cut buffer, the client-wide text saved by input-cut actions and used by the `yank_from_cutbuffer` key action. It is shared across screens and windows, rather than belonging to the current input line or the last-input screen.

`get` returns the saved text. `set` replaces it with the entire remaining input and returns the replacement. Omitting text clears the buffer. Text is stored literally after normal function-call expansion; surrounding quotes or braces are not removed. Additional separator spaces after `cutbuffer` are part of the stored text.

Operation and property names are case-insensitive and accept prefixes. They are function argument words, with quote handling governed by `/xdebug dword`. Extra arguments to `get` are ignored.

## Practical

Use this function to inspect saved cut text or supply replacement text for a later yank action. Setting the cut buffer does not insert text, move the cursor, redraw the input line, or submit a command. A later cut action replaces the saved text again.

## Returns

The saved or newly stored text, without added quoting. A never-set or cleared buffer returns empty. Missing required operation/property arguments, unknown operations, and unsupported properties also return empty without a diagnostic; these cases cannot be distinguished from an empty buffer by the result alone.

## Restrictions

`cutbuffer` is the only supported property. There are no screen selectors, cursor controls, input-history operations, or undo operations in this built-in. Use [$curpos()](curpos.md) to query the cursor position, [type](../commands/type.md) to simulate keystrokes, or [xtype -literal](../commands/xtype.md) to insert text at the cursor without invoking key bindings.

## Examples

```irc
@ saved_cut_text = inputctl(get cutbuffer)
@ inputctl(set cutbuffer replacement text)
@ inputctl(set cutbuffer)
```

The first stores a copy of the buffer in a script variable. The next two replace it and then clear it; neither edits the visible input line.

## Source

Registered in [source/functions.c](../../source/functions.c). Control operations, input cutting, and yanking: [source/input.c](../../source/input.c). Global buffer initialization: [source/irc.c](../../source/irc.c). Key-action registration and default binding: [source/keys.c](../../source/keys.c).
