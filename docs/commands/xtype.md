# xtype command

## Synopsis

```text
/xtype text
/xtype -literal text
```

## Description

Without an option, `xtype` behaves like [`type`](type.md): it simulates keystrokes, interprets caret notation and backslash quoting, and allows key bindings to perform actions. With no text, it does nothing.

With `-literal`, each character of the text is inserted directly into the input line, ignoring key bindings, as though it were handled by the `self_insert` action. Insertion starts at the current cursor and advances it, respecting [`insert_mode`](../settings/insert_mode.md): characters are inserted before existing text when that setting is on and replace existing characters when it is off. At the end of the line, they are appended.

Unlike `type`, literal mode inserts a caret followed by another character as two characters; `^A` does not become Control-A. It also preserves backslashes instead of using them to quote the following character. Actual control characters bypass their key bindings too, so inserting a newline does not invoke the usual submit-line binding.

Literal text is decoded as UTF-8 directly, rather than through the console encoding used by `type`. Bytes the decoder cannot recognize are skipped. Normal script expansion still occurs before this command receives its arguments; `-literal` does not protect dollar expressions from that expansion. The command itself does not strip quotes or braces from the text.

## Options

| Option | Effect |
| --- | --- |
| `-literal text` | Insert the remaining text directly, bypassing key bindings and `type` notation. `-l` is sufficient. |

The option is recognized only at the start of the arguments. A slash can replace its leading hyphen (`/literal`), and any option name beginning with `l`, case-insensitively, selects literal mode. Everything after that option is text, including further apparent options. An unknown initial option displays `Unknown flag -name to XTYPE` and inserts nothing; there is no `--` terminator. A lone `-` or `/` is inserted literally.

## Effects and restrictions

This command edits the active input line and returns no value. Literal mode bypasses input-prompt key dispatch as well as bindings; use [`input_char`](input_char.md) for a prompted response rather than relying on literal insertion to answer it. Direct editing needs no IRC connection. In the unflagged form, actions triggered by bindings can execute commands or send messages.

## Examples

To insert a caret followed by `A` instead of performing the Control-A binding:

```text
/xtype -l now inserting a ^A is easy!
```

To insert text containing a backslash:

```text
/xtype -literal path\name
```

These examples edit the input line without submitting it.

## Source

[source/commands.c](../../source/commands.c) registers `xtype` and implements `xtypecmd()` and its unflagged delegation to `typecmd()`. [source/input.c](../../source/input.c) implements direct character insertion and cursor movement. [source/wcwidth.c](../../source/wcwidth.c) implements UTF-8 decoding.
