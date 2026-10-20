# Default key bindings

## Synopsis

```text
/bind
/bind -defaults
```

These are the core bindings created at client startup and rebuilt by `bind -defaults`. Terminal capability bindings are then added, and startup scripts can replace them. The bindings active in an ordinary configured session can therefore differ from these initial defaults. Use [bind](../commands/bind.md) to inspect the current map.

`^A` means Control-A; `^[` means Escape; `^?` means DEL, byte 127. Characters following Escape are a sequence, not necessarily simultaneous keystrokes. Ordinary letter case matters in sequences: Escape followed by lowercase `b` is different from Escape followed by uppercase `B`.

## Single-character bindings

Printable ASCII characters, including space, initially use [self_insert](../bind/self_insert.md). DEL is subsequently rebound to backspace. Non-ASCII input characters use the input system's Unicode insertion path rather than an ordinary byte-sequence binding.

| Sequence | Bound action |
| --- | --- |
| `^A` | [beginning_of_line](../bind/beginning_of_line.md) |
| `^B` | [bold](../bind/bold.md) |
| `^C` | [self_insert](../bind/self_insert.md) |
| `^D` | [delete_character](../bind/delete_character.md) |
| `^E` | [end_of_line](../bind/end_of_line.md) |
| `^F` | [blink](../bind/blink.md) |
| `^G` | [self_insert](../bind/self_insert.md) |
| `^H` | [backspace](../bind/backspace.md) |
| `^I` | [toggle_insert_mode](../bind/toggle_insert_mode.md) |
| `^J` | [send_line](../bind/send_line.md) |
| `^K` | [erase_to_end_of_line](../bind/erase_to_end_of_line.md) |
| `^L` | [refresh_screen](../bind/refresh_screen.md) |
| `^M` | [send_line](../bind/send_line.md) |
| `^O` | [highlight_off](../bind/highlight_off.md) |
| `^Q` | [quote_character](../bind/quote_character.md) |
| `^U` | [erase_line](../bind/erase_line.md) |
| `^V` | [reverse](../bind/reverse.md) |
| `^W` | [next_window](../bind/next_window.md) |
| `^X` | [switch_channels](../bind/switch_channels.md) |
| `^Y` | [yank_from_cutbuffer](../bind/yank_from_cutbuffer.md) |
| `^_` | [underline](../bind/underline.md) |
| `^?` | [backspace](../bind/backspace.md) |

Tab is `^I`, so its core action toggles insert mode rather than performing completion. Enter commonly sends `^M` or `^J`, both bound to `send_line`. A physical Backspace key can send `^H` or DEL; both erase backward here. A terminal's separate Delete key may have a multi-character sequence mapped to forward deletion instead.

Control-C has a `self_insert` binding if that character reaches the binding system. Fullscreen terminal setup normally leaves Control-C as the terminal's interrupt signal, so the binding should not be interpreted as a guarantee that pressing Control-C inserts text. Control-G inserts the bell character. Neither action itself submits the line.

No core action is assigned to Control-N, Control-P, Control-R, Control-S, Control-T, or Control-Z. In particular, there is no default stop-screen, transpose, or process-suspend action on those keys. Scripts can assign other behavior.

## Escape sequences

| Sequence | Bound action |
| --- | --- |
| `^[.` | [clear_screen](../bind/clear_screen.md) |
| `^[<` | [scroll_start](../bind/scroll_start.md) |
| `^[>` | [scroll_end](../bind/scroll_end.md) |
| `^[b` | [backward_word](../bind/backward_word.md) |
| `^[d` | [delete_next_word](../bind/delete_next_word.md) |
| `^[e` | [scroll_end](../bind/scroll_end.md) |
| `^[f` | [forward_word](../bind/forward_word.md) |
| `^[h` | [delete_previous_word](../bind/delete_previous_word.md) |
| `^[n` | [scroll_forward](../bind/scroll_forward.md) |
| `^[p` | [scroll_backward](../bind/scroll_backward.md) |
| `^[^?` | [delete_previous_word](../bind/delete_previous_word.md) |

## Fixed terminal-style sequences

The following byte sequences are also installed directly. Their physical key names depend on the terminal or emulator; the sequence spelling is the reliable identifier.

| Sequence | Bound action |
| --- | --- |
| `^[OC`, `^[[C` | [forward_character](../bind/forward_character.md) |
| `^[OD`, `^[[D` | [backward_character](../bind/backward_character.md) |
| `^[OF`, `^[[F`, `^[O4~`, `^[[4~` | [scroll_end](../bind/scroll_end.md) |
| `^[OG`, `^[[G`, `^[O6~`, `^[[6~` | [scroll_forward](../bind/scroll_forward.md) |
| `^[OH`, `^[[H`, `^[O1~`, `^[[1~` | [scroll_start](../bind/scroll_start.md) |
| `^[OI`, `^[[I`, `^[O5~`, `^[[5~` | [scroll_backward](../bind/scroll_backward.md) |
| `^[On`, `^[[n` | [next_window](../bind/next_window.md) |
| `^[Op`, `^[[p` | [previous_window](../bind/previous_window.md) |

The core table does not assign Up/Down history actions. The supplied [history](../scripts/history.md) script can install history navigation, while completion scripts can replace Tab and other keys. An external terminal or multiplexer can intercept a key before EPIC receives it.

## Terminal-derived bindings

After core setup, `init_termkeys` uses terminal capabilities to add these mappings when the corresponding capability exists:

| Terminal capability | Action |
| --- | --- |
| `key_ppage` | `scroll_backward` |
| `key_npage` | `scroll_forward` |
| `key_home` | `scroll_start` |
| `key_end` | `scroll_end` |
| `key_ic` | `toggle_insert_mode` |
| `key_dc` | `delete_character` |

These can overlap or replace fixed sequences. Correct `TERM` and terminal capability data matter; not every keyboard sends the same sequence for Home, End, Insert, Delete, or Page Up/Down. The terminal setup disables XON/XOFF flow control in fullscreen mode, but surrounding terminal or session configuration can still affect delivery.

## Resetting and customizing

`bind -defaults` removes existing binding actions, recreates built-in actions, constructs the default keymap, and reapplies terminal-capability mappings. It does not rerun your startup scripts to reinstall history, completion, or custom key actions. Script-created bind functions and prior assignments are not automatically restored.

To make a single change, bind just the desired key rather than resetting all bindings. For example:

```text
/bind ^I self_insert
```

This makes Tab insert a literal tab if it reaches EPIC. Individual action pages describe cursor movement, cutbuffer changes, formatting insertion, window switching, and message submission. A key binding has no general function-return value; its effects come from its assigned action. Core bindings need no optional script package or server extension, though actions involving a target can depend on connection state.

## Source references

- [source/keys.c](../../source/keys.c): `init_binds`, `init_keys`, `init_termkeys`, key-sequence parsing, and `bind -defaults` reset path.
- [source/term.c](../../source/term.c): terminal capabilities, interrupt-key setup, and flow-control handling.
- [source/input.c](../../source/input.c): character insertion, cursor editing, and submission.
- [script/history](../../script/history): startup-script history bindings beyond the core defaults.
