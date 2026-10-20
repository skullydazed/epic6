# How to use key bindings in EPIC

The [bind](../commands/bind.md) command associates a key or sequence of keys with an action. Actions can edit input, move between windows, submit a line, or execute script commands. Bindings operate on input delivered by the terminal; they do not identify physical keyboard keys independently of the terminal's sequences.

## Sequence notation

ASCII characters and sequences can be bound using the following notation:

| Sequence | Description |
| --- | --- |
| `c` | A literal ASCII character; uppercase and lowercase are distinct. |
| `^A` through `^Z` | Control letters; lowercase control notation such as `^a` means the same control character. |
| `^@`, `^[`, `^\`, `^]`, `^^`, `^_` | Control bytes 0, 27, 28, 29, 30, and 31. |
| `^?` | Delete, byte 127. |
| `\e` | Escape, byte 27. |
| `\c` | Escape a literal character; octal digits and lowercase `e` have special meanings. Use `\^` for a literal caret and `\\` for a literal backslash. |
| `\nnn` | One to three octal digits; the result is reduced to its low seven bits. |
| A combination of these | A sequence of input characters, such as `^X^E` or `^[[D`. |

Invalid caret notation is treated literally. Literal high-bit bytes cannot be bound, and Unicode code points above 127 go directly to input insertion rather than normal binding lookup. For example, `\200` denotes byte zero, not a non-ASCII character. EPIC's script parser can also process backslashes before a sequence reaches `bind`; take that extra expansion pass into account when putting bindings in scripts.

## Choosing an action

```irc
/bind ^[[D backward_character
/bind ^X^E parse_command { echo Key pressed; }
```

The first example makes Escape, `[`, `D` move the input cursor backward. The second runs an EPIC command when Control-X followed by Control-E is pressed. Multiple commands belong inside the braces and are separated by semicolons.

Action names are case-insensitive and must name registered binding actions. An arbitrary command or alias name is not automatically an action: use [parse_command](../bind/parse_command.md) to run script text, or [bindctl](../functions/bindctl.md) to register an alias-backed action. An unknown action reports an error and leaves the old binding unchanged. Any trailing argument text is stored for the selected action; some actions use it and others ignore it.

`parse_command` evaluates its stored text when the key is pressed, with an empty argument string. It does not automatically supply the current input line as `$*` or submit that line. Commands it runs have their own output, errors, and local or network effects.

## Inspecting and changing bindings

| Command | Effect |
| --- | --- |
| `/bind` | List bindings in the current keymap, omitting ordinary self-insert entries. |
| `/bind ^X^E` | Show the binding for that sequence. |
| `/bind -recursive ^X` | Show bindings beneath that prefix. |
| `/bind ^X^E nothing` | Remove that exact sequence's binding; longer sequences can remain. |
| `/bind -symbolic key_left backward_character` | Use the sequence advertised by the terminal description's `key_left` capability. |
| `/bind -defaults` | Reset actions and bindings to the built-in defaults and terminal-derived initialization. |

Symbolic capability names are the long names from EPIC's terminal table. An unsupported capability produces a diagnostic. The defaults can be changed by startup scripts; see [default keybindings](default_keybindings.md). Binding changes affect the running client, so put desired customizations in a startup script to recreate them in a later session.

## Sequences and timing

A sequence can be both a complete binding and the prefix of a longer one. EPIC waits at an ambiguous prefix to see whether more input completes the longer sequence. [key_interval](../set/key_interval.md) controls this timeout in milliseconds; values below 10 are raised to 10. Unmatched sequences can fall back to shorter component bindings, and unbound characters can be discarded.

The terminal emulator, multiplexer, or operating system may intercept a shortcut or send a different sequence. A binding cannot act on input EPIC never receives. [On keybinding](../on/keybinding.md) can also suppress a matched action. See [input handling](input_handling.md) for character decoding, prompt interception, and submission of edited input.

## Source references

- `source/keys.c`: notation parsing, action registration, sequence matching, timeout handling, and `bind`.
- `source/screen.c`: console decoding and dispatch to keybinding lookup.
- `source/input.c`: editing and input-submission actions.
- `source/term.c`: terminal capability lookup.
- `source/hook.c`: keybinding hook dispatch rules.
