# set status_mode

## Synopsis

```text
/set status_mode [<text>]
/set -status_mode
```

Default: ` (+%+)`, including a leading space. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the mode field's format rather than restoring the default.

## Description

Sets the format of the current channel's cached mode string in the status bar. The normal `%+` field in [status_format](status_format.md) appears only when that string is nonempty. It represents channel modes rather than the per-nickname operator and voice indicators, which have separate fields.

The first `%+` inside `status_mode` is replaced with the mode text before insertion into the outer status line. The cached mode string includes retained arguments such as the channel limit and key; the default wrapper supplies its own leading `+`. Other status percent fields are not recursively interpreted in this wrapper.

## Mapped variants

All these outer fields use the same `status_mode` wrapper, with plain `%+` inside it as the substitution point:

| Outer field | Mode value and visibility |
| --- | --- |
| `%+` | Mode letters and retained arguments; empty field when mode text is empty. |
| `%{1}+` | Same value, but allows an empty mode string. |
| `%{2}+` | Only the first word, omitting arguments; empty field when mode text is empty. |
| `%{3}+` | Only the first word, allowing an empty mode string. |

Every variant requires the window to have a server. The variants allowing empty mode text can show the wrapper even when there is no current channel; with the default wrapper, an empty value produces ` (+)`.

## Effects and limitations

The outer global or per-window format must contain an applicable field. Each window uses its own channel state; [show_status_all](show_status_all.md) does not impose a current-window restriction on it. The client displays its cached representation, not a freshly requested or necessarily exhaustive list of every server-specific mode and argument.

Changing this setting rebuilds status formats and requests updates. It does not change channel modes or send an IRC request. Ordinary command parsing can expand dollar expressions on assignment; [status_does_expandos](status_does_expandos.md) optionally expands the assembled status line later. The shared wrapper helper escapes dollar signs and backslashes in the inserted mode value for that later pass, including characters in a channel key.

## Example

```text
/set status_mode Mode +%+
```

With a cached mode value of `nt`, an outer `%+` supplies `Mode +nt`. To omit arguments, use `%{2}+` in the outer status format while retaining `%+` in this wrapper. Hide all variants by unsetting `status_mode`.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, rebuild callback, and default wrapper.
- [source/status.c](../../source/status.c): mapped `%+` entries, wrapper compilation, branch-specific empty handling, argument omission, and `PRESS` escaping.
- [source/names.c](../../source/names.c): cached channel mode construction and `get_channel_mode`.
