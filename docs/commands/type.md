# type command

## Synopsis

```text
/type keystrokes
```

## Description

`type` simulates user keystrokes. Ordinary characters normally appear in the input line, while bound keys perform their usual actions. Text is inserted at the current cursor, respecting the [`insert_mode`](../settings/insert_mode.md) setting; it is not automatically appended at the end.

The argument text uses caret notation for control keys: `^A` through `^Z` represent their control characters, `^[` represents Escape, and `^?` represents Delete. Lowercase letters after a caret are converted to uppercase. A backslash quotes the next character for this notation parser: `\^` types a caret and `\\` types a backslash. This quoting does not bypass key bindings. A final caret or backslash without a following character is ignored. An invalid caret sequence such as `^1` displays `Invalid key sequence: ^1` and stops, leaving earlier keystrokes already processed.

Variables may be expanded by the script command parser before `type` receives its arguments. Quotes and braces are not removed by `type` itself, and there are no options. With no arguments, it does nothing. For literal character insertion that bypasses key bindings and caret notation, use [`xtype -literal`](xtype.md); bare `xtype` behaves like `type`.

The simulated bytes pass through the same console decoding and input dispatch as physical keyboard input. Set [`encoding console`](encoding.md) appropriately; non-ASCII text can be misinterpreted if its bytes do not match that encoding. ASCII keys use the current [`bind`](bind.md) mappings, including macro bindings and the keybinding hook. Decoded characters above ASCII are inserted directly. Pending key sequences and quoted-key state can also affect the result.

## Effects and restrictions

This command edits the active input line rather than returning a value. A binding may instead move the cursor, run an alias, or perform another action. For example, `^M` normally submits the input line and can execute a command or send a message. Active input prompts receive simulated keys too; an [`input_char`](input_char.md) prompt consumes the next character as its response.

The normal input dispatcher requires fullscreen mode; simulated input is discarded outside that mode. Ordinary editing does not require an IRC connection, but actions triggered by bindings may require one.

## Examples

With the default bindings, this types a kick template and moves the cursor backward two words so you can edit it before submitting:

```text
/type /kick FooScript C1996^[b^[b
```

Here `^[b` is Escape followed by `b`, normally bound to backward-word movement. Different bindings can change the result. This template does not submit the command automatically.

With the default `^E` binding, this moves to the end of the existing input before adding text:

```text
/type ^E additional text
```

## Source

[source/commands.c](../../source/commands.c) registers `type` and implements `typecmd()` and `xtypecmd()`. [source/screen.c](../../source/screen.c) decodes console bytes and dispatches input and prompts. [source/keys.c](../../source/keys.c) implements binding dispatch and default bindings; [source/input.c](../../source/input.c) implements character insertion and cursor editing.
