# set status_user

## Synopsis

```text
/set status_user [<text>]
/set -status_user
```

The compiled default is an EPIC6 help-site message. With no text, [set](../commands/set.md) displays the current value. Unsetting removes its text rather than restoring the default. The numbered `status_user1` through `status_user39` fields default to empty strings.

## Description

Provides user-defined text for arbitrary information in the status bar. `status_user` is represented by `%U` or `%0` in [status_format](status_format.md), or a per-window format. Those two fields read the same setting and cannot be configured independently.

The value is inserted directly. `%U` or `%0` inside `status_user` has no nested substitution meaning. The outer format must contain an applicable field; assigning text does not add it to the format automatically.

With [show_status_all](show_status_all.md) off, `status_user` appears only in the input window of each screen. Turning it on permits the field in other windows too. The stored string is global, not a separate value for each window.

## Numbered fields and aliases

| Setting | Outer field | Additional alias |
| --- | --- | --- |
| `status_user` | `%0` | `%U` |
| `status_user1` | `%1` | `%X` |
| `status_user2` | `%2` | `%Y` |
| `status_user3` | `%3` | `%Z` |
| `status_user4` through `status_user9` | `%4` through `%9` | — |
| `status_user10` through `status_user19` | `%{1}0` through `%{1}9` | — |
| `status_user20` through `status_user29` | `%{2}0` through `%{2}9` | — |
| `status_user30` through `status_user39` | `%{3}0` through `%{3}9` | — |

Maps zero and one use the current-window visibility rule. Maps two and three display independently of it. These are separate numbered settings; the listed duplicate fields are aliases for the same stored values.

## Expansion and effects

Ordinary command parsing can expand dollar expressions when assigning the value. To evaluate variables or functions during later status regeneration, preserve their dollar signs on assignment and enable [status_does_expandos](status_does_expandos.md). That pass uses the displayed window's context and an empty argument string; it does not provide an independent refresh timer.

Changing the setting rebuilds status formats and requests updates. It does not execute the text as a command block or send an IRC request. Without the optional expansion pass, a stored dollar expression remains text. Percent status fields are not recursively parsed within it.

## Examples

Set a fixed status label:

```text
/set status_user Client configured by IRC Systems, Inc.
```

With `%U` or `%0` in the outer format, the label appears subject to visibility rules. A dynamic nickname label can instead be stored with:

```text
/set status_user $$N
/set status_does_expandos on
```

Use `/set -status_user` to hide the field without changing the outer status format.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): user-field registration, rebuild callbacks, and defaults.
- [source/status.c](../../source/status.c): field table, `status_user` map lookup and visibility, direct insertion, and optional contextual dollar expansion.
